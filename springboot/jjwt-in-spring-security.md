---
description: Using Java Json Web Token library for JWT in Spring Boot and Security
---

# JJWT in Spring Security

## What is JWT?

JWT là một chuỗi 3 phần mang theo thông tin (claims).

Ví dụ:

{% code lineNumbers="true" %}
```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.
eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiYWRtaW4iOnRydWV9.
TJVA95OrM7E2cBab30RMHrHDcEfxjoYZgeFONFh7HgQ
```
{% endcode %}

JWT rất compact (gọn gàngàng, súc tích). Biểu diễn một chuỗi các thông tin (claims) and signature.

```json
{
	"alg": "HS256",
	"typ": "JWT"
}
{
	"sub": "1234567890",
	"name": "John Doe",
	"admin": true
}
```

### Các ứng dụng của JWT

* Authentication
* Authorization
* Federated identity (Nhận dạng liên kết)
* Client-side session (”stateless” session)
* Client-side secrets

### Challenges

* Revoke token
* Refresh token, rotate token

## JJWT

Java Json Web Token Library (≥ v0.12.x)

### Dependencies

`Maven`

{% code title="pom.xml" %}
```xml
<!-- Core JJWT API -->
<dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt-api</artifactId>
    <version>0.12.6</version>
</dependency>

<!-- JJWT Implementation -->
<dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt-impl</artifactId>
    <version>0.12.6</version>
    <scope>runtime</scope>
</dependency>

<!-- JSON processor (Jackson) -->
<dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt-jackson</artifactId>
    <version>0.12.6</version>
    <scope>runtime</scope>
</dependency>

<!-- Spring Boot Security -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```
{% endcode %}

Or `Gradle`

{% code title="build.gradle.kts" %}
```kotlin
// Core JJWT API
implementation("io.jsonwebtoken:jjwt-api:0.12.6")

// JJWT Implementation (at runtime)
runtimeOnly("io.jsonwebtoken:jjwt-impl:0.12.6")

// JSON processor (Jackson)
runtimeOnly("io.jsonwebtoken:jjwt-jackson:0.12.6")

// Spring Boot Security
implementation("org.springframework.boot:spring-boot-starter-security")

```
{% endcode %}

### Code JJWT

#### 1. Secret Key

Using raw-key utf8 in `application.properties`

```java
public class JwtService {
    public static final long JWT_EXPIRATION = 60 * 60 * 24 * 1000L;
    private final Key key;

    public JwtService(@Value("${jwt.secret.key}") String secretKey) {
        //        byte[] keysBytes = Decoders.BASE64.decode(SECRET_KEY);
        //        this.key = Keys.hmacShaKeyFor(keysBytes);
        this.key = Keys.hmacShaKeyFor(secretKey.getBytes(StandardCharsets.UTF_8));
    }
}
```

#### 2. Generate Token

From user details

```java
public String generateToken(UserDetails userDetails) throws JwtTokenCreationException {
    return generateToken(new HashMap<>(), userDetails);
}
```

With extra claims

```java
 public String generateToken(Map<String, Object> extraClaims, UserDetails userDetails) throws JwtTokenCreationException {
        if (userDetails == null) {
            throw new IllegalArgumentException("UserDetails cannot be null");
        }
        if (extraClaims == null) {
            extraClaims = new HashMap<>();
        }

        Map<String, Object> claims = new HashMap<>(extraClaims);

        if (userDetails instanceof CustomUserDetails customUserDetails) {
            claims.put("roles", customUserDetails.getAuthorities().stream().map(GrantedAuthority::getAuthority).collect(Collectors.toSet()));
            claims.put("id", customUserDetails.getId());
        }
        return buildToken(claims, userDetails);
    }
```

Build token (create jwt)

```java
public String buildToken(Map<String, Object> claims, UserDetails userDetails) throws JwtTokenCreationException {
        try {
            return Jwts.builder().header().add(
        createHeader()).and()
        .claims(claims).subject(userDetails.getUsername()).issuedAt(new Date(System.currentTimeMillis())).expiration(new Date(System.currentTimeMillis() + JWT_EXPIRATION)).signWith(key).compact();
    } catch (Exception e) {
        throw new JwtTokenCreationException("Failed to generate JWT token", e);
    }
}

private Map<String, Object> createHeader() {
    Map<String, Object> header = new HashMap<>();
    header.put("typ", "JWT");
    header.put("alg", "HS256");
    return header;
}
```

#### Extract infomation from token

Include extract All and extract single claim.

```java
public Optional<String> extractUsername(String token) {
    try {
        return Optional.ofNullable(extractClaim(token, Claims::getSubject));
    } catch (ExpiredJwtException e) {
        log.warn("Token expired: {}", e.getMessage());
        return Optional.empty();
    } catch (InvalidJWTException e) {
        log.warn("Invalid JWT: {}", e.getMessage());
        return Optional.empty();
    }
}

public Long extractUserId(String token) {
    try {
        Claims claims = extractAllClaims(token);
        Object id = claims.get("id");
        if (id instanceof Number) {
            return ((Number) id).longValue();
        } else if (id instanceof String) {
            return Long.parseLong((String) id);
        }
    } catch (Exception e) {
        log.warn("Failed to extract user ID from token: {}", e.getMessage());
    }
    return null;
}

public Date extractExpiration(String token) {
    return extractClaim(token, Claims::getExpiration);
}

public <T> T extractClaim(String token, Function<Claims, T> claimsResolver) {
    final Claims claims = extractAllClaims(token);
    return claimsResolver.apply(claims);
}

public Claims extractAllClaims(String token) throws InvalidJWTException, ExpiredJwtException {
    if (token == null || token.trim().isEmpty()) {
        throw new InvalidJWTException("Invalid JWT token");
    }

    try {
        return Jwts.parser().verifyWith((SecretKey) key).build().parseSignedClaims(token).getPayload();
    } catch (Exception e) {
        log.warn("JWT token expired: {}", e.getMessage());
        throw new InvalidJWTException("JWT token invalid or expired", e);
    }
}

```

#### Validate token

Validate token with general exception try-catch.

```java
private boolean isTokenExpired(String token) {
    return extractExpiration(token).before(new Date());
}

public boolean isTokenValid(String token) {
    try {
        Claims claims = extractAllClaims(token);
        return !claims.getExpiration().before(new Date());
    } catch (Exception ex) {
        log.error("JWT invalid signature for token");
        return false;
    }
}
```

### Authentication Entry Point

{% code title="JWtAuthenticationEntryPoint.java" %}
```java
@Component
public class JwtAuthenticationEntryPoint implements AuthenticationEntryPoint {
    @Override
    public void commence(HttpServletRequest request, HttpServletResponse response,
            AuthenticationException authException) throws IOException {
        response.sendError(HttpServletResponse.SC_UNAUTHORIZED, "Chua Xac thuc");
    }
}
```
{% endcode %}

### JwtAuthenticationFilter

{% code title="JwtAuthenticationFilter.java" %}
```java
@Component
public class JwtAuthenticationFilter extends OncePerRequestFilter {

    private final JwtService jwtService;

    private final CustomUserDetailsService userDetailsService;

    public JwtAuthenticationFilter(JwtService jwtService, CustomUserDetailsService userDetailsService) {
        this.jwtService = jwtService;
        this.userDetailsService = userDetailsService;
    }

    @Override
    protected void doFilterInternal(HttpServletRequest request,
                                    HttpServletResponse response,
                                    FilterChain filterChain) throws ServletException, IOException {

        String token = extractTokenFromHeader(request);

        if (token != null && SecurityContextHolder.getContext().getAuthentication() == null) {
            try {
                // 1. Validate token signature + expiration
                if (!jwtService.isTokenValid(token)) {
                    log.warn("JWT is invalid or expired");
                    response.sendError(HttpServletResponse.SC_UNAUTHORIZED, "Invalid JWT token");
                    return;
                }

                // 2. Extract username
                Optional<String> usernameOpt = jwtService.extractUsername(token);
                if (usernameOpt.isEmpty()) {
                    log.warn("JWT does not contain username");
                    response.sendError(HttpServletResponse.SC_UNAUTHORIZED, "Invalid JWT token");
                    return;
                }
                String username = usernameOpt.get();

                // 3. Extract roles
                List<String> roles = jwtService.extractClaim(token, claims -> {
                    Object rolesObj = claims.get("roles");
                    if (rolesObj instanceof List<?>) {
                        return ((List<?>) rolesObj).stream()
                                .filter(String.class::isInstance)
                                .map(String.class::cast)
                                .collect(Collectors.toList());
                    }
                    return Collections.emptyList();
                });

                List<SimpleGrantedAuthority> authorities = roles.stream()
                        .map(SimpleGrantedAuthority::new)
                        .toList();

                UsernamePasswordAuthenticationToken authToken =
                        new UsernamePasswordAuthenticationToken(username, null, authorities);
                authToken.setDetails(new WebAuthenticationDetailsSource().buildDetails(request));
                SecurityContextHolder.getContext().setAuthentication(authToken);

            } catch (Exception e) {
                log.error("Failed to authenticate JWT token: {}", e.getMessage());
                response.sendError(HttpServletResponse.SC_UNAUTHORIZED, "Invalid JWT token");
                return;
            }
        }

        filterChain.doFilter(request, response);
    }

    private String extractTokenFromHeader(HttpServletRequest request) {
        String authHeader = request.getHeader("Authorization");
        return (authHeader != null && authHeader.startsWith("Bearer "))
                ? authHeader.substring(7)
                : null;
    }
}
```
{% endcode %}

### Register Filter and EntryPoint to SecurityConfig



***

## Q & N

{% hint style="warning" %}
I change final character of jwt and it still valid?

Because it is padding of base64. But your jwt is still original content after decode, each jwt is unique.
{% endhint %}
