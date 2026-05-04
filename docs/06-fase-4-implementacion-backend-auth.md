# Fase 4: Implementación - Módulo de Autenticación Backend

**Objetivo:** Implementar el módulo de autenticación (Auth) en el backend Spring Boot 3.5 como base para todas las operaciones siguientes.

**Alcance:**

- Setup e inicialización del proyecto
- Estructura de paquetes y capas
- Entidades y DTOs
- Configuración de seguridad
- Endpoints JWT
- Validaciones y manejo de errores

**Duración estimada:** 3-4 sprints (3-4 semanas)

---

## 1. Setup Inicial del Proyecto

### 1.1 Estructura de Paquetes

```
hospital-familia-server/src/main/java/com/hospitalfamilia/
├── config/
│   ├── SecurityConfig.java
│   ├── JwtConfig.java
│   └── CorsConfig.java
├── auth/
│   ├── controller/
│   │   └── AuthController.java
│   ├── service/
│   │   ├── AuthService.java
│   │   ├── UserDetailsServiceImpl.java
│   │   └── JwtTokenProvider.java
│   ├── entity/
│   │   ├── User.java
│   │   ├── Role.java
│   │   └── Permission.java
│   ├── repository/
│   │   ├── UserRepository.java
│   │   ├── RoleRepository.java
│   │   └── PermissionRepository.java
│   ├── dto/
│   │   ├── LoginRequest.java
│   │   ├── LoginResponse.java
│   │   ├── RegisterRequest.java
│   │   ├── UserDTO.java
│   │   └── TokenRefreshRequest.java
│   ├── security/
│   │   ├── JwtAuthenticationFilter.java
│   │   ├── JwtAuthenticationEntryPoint.java
│   │   └── CustomAccessDeniedHandler.java
│   └── exception/
│       ├── AuthenticationException.java
│       ├── TokenException.java
│       └── UserAlreadyExistsException.java
├── common/
│   ├── entity/
│   │   └── BaseEntity.java
│   ├── dto/
│   │   └── ApiResponse.java
│   ├── exception/
│   │   ├── GlobalExceptionHandler.java
│   │   └── BusinessException.java
│   └── constants/
│       ├── ErrorMessages.java
│       └── AppConstants.java
└── HospitalFamiliaServerApplication.java
```

### 1.2 Configuración Maven (pom.xml)

**Dependencias a agregar/verificar:**

```xml
<dependencies>
    <!-- Spring Security & JWT -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-security</artifactId>
    </dependency>
    <dependency>
        <groupId>io.jsonwebtoken</groupId>
        <artifactId>jjwt-api</artifactId>
        <version>0.12.3</version>
    </dependency>
    <dependency>
        <groupId>io.jsonwebtoken</groupId>
        <artifactId>jjwt-impl</artifactId>
        <version>0.12.3</version>
        <scope>runtime</scope>
    </dependency>
    <dependency>
        <groupId>io.jsonwebtoken</groupId>
        <artifactId>jjwt-jackson</artifactId>
        <version>0.12.3</version>
        <scope>runtime</scope>
    </dependency>

    <!-- Spring Data JPA -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>

    <!-- PostgreSQL Driver -->
    <dependency>
        <groupId>org.postgresql</groupId>
        <artifactId>postgresql</artifactId>
        <version>42.7.1</version>
    </dependency>

    <!-- Validation -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-validation</artifactId>
    </dependency>

    <!-- Lombok (opcional, pero recomendado) -->
    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
        <optional>true</optional>
    </dependency>
</dependencies>
```

---

## 2. Entidades y Modelos

### 2.1 BaseEntity.java

```java
package com.hospitalfamilia.common.entity;

import jakarta.persistence.*;
import lombok.Getter;
import lombok.Setter;
import java.time.LocalDateTime;

@MappedSuperclass
@Getter
@Setter
public abstract class BaseEntity {

    @Id
    @GeneratedValue(strategy = GenerationType.SEQUENCE, generator = "id_generator")
    @SequenceGenerator(name = "id_generator", sequenceName = "seq_id", allocationSize = 1)
    private Long id;

    @Column(nullable = false, updatable = false)
    private LocalDateTime createdAt = LocalDateTime.now();

    @Column(nullable = false)
    private LocalDateTime updatedAt = LocalDateTime.now();

    @Column(length = 50)
    private String createdBy;

    @Column(length = 50)
    private String updatedBy;

    @PreUpdate
    protected void onUpdate() {
        this.updatedAt = LocalDateTime.now();
    }
}
```

### 2.2 Role.java

```java
package com.hospitalfamilia.auth.entity;

import com.hospitalfamilia.common.entity.BaseEntity;
import jakarta.persistence.*;
import lombok.AllArgsConstructor;
import lombok.Getter;
import lombok.NoArgsConstructor;
import lombok.Setter;
import java.util.HashSet;
import java.util.Set;

@Entity
@Table(name = "roles", uniqueConstraints = @UniqueConstraint(columnNames = "name"))
@Getter
@Setter
@NoArgsConstructor
@AllArgsConstructor
public class Role extends BaseEntity {

    @Column(nullable = false, unique = true, length = 50)
    private String name; // TUTOR, PACIENTE, DOCTOR, ADMIN

    @Column(length = 255)
    private String description;

    @ManyToMany(fetch = FetchType.EAGER)
    @JoinTable(
        name = "role_permissions",
        joinColumns = @JoinColumn(name = "role_id"),
        inverseJoinColumns = @JoinColumn(name = "permission_id")
    )
    private Set<Permission> permissions = new HashSet<>();

    @Column(nullable = false)
    private Boolean active = true;
}
```

### 2.3 Permission.java

```java
package com.hospitalfamilia.auth.entity;

import com.hospitalfamilia.common.entity.BaseEntity;
import jakarta.persistence.*;
import lombok.AllArgsConstructor;
import lombok.Getter;
import lombok.NoArgsConstructor;
import lombok.Setter;

@Entity
@Table(name = "permissions", uniqueConstraints = @UniqueConstraint(columnNames = "code"))
@Getter
@Setter
@NoArgsConstructor
@AllArgsConstructor
public class Permission extends BaseEntity {

    @Column(nullable = false, unique = true, length = 100)
    private String code; // ej: "READ_PATIENT", "WRITE_EVENT"

    @Column(length = 255)
    private String description;

    @Column(nullable = false)
    private String resource; // "PATIENT", "EVENT", "USER"

    @Column(nullable = false)
    private String action; // "READ", "WRITE", "DELETE"
}
```

### 2.4 User.java

```java
package com.hospitalfamilia.auth.entity;

import com.hospitalfamilia.common.entity.BaseEntity;
import jakarta.persistence.*;
import lombok.AllArgsConstructor;
import lombok.Getter;
import lombok.NoArgsConstructor;
import lombok.Setter;
import java.util.HashSet;
import java.util.Set;

@Entity
@Table(name = "users", uniqueConstraints = {
    @UniqueConstraint(columnNames = "email"),
    @UniqueConstraint(columnNames = "username")
})
@Getter
@Setter
@NoArgsConstructor
@AllArgsConstructor
public class User extends BaseEntity {

    @Column(nullable = false, unique = true, length = 50)
    private String username;

    @Column(nullable = false, unique = true, length = 120)
    private String email;

    @Column(nullable = false)
    private String password; // bcrypted

    @Column(length = 100)
    private String firstName;

    @Column(length = 100)
    private String lastName;

    @Column(length = 20)
    private String phoneNumber;

    @ManyToMany(fetch = FetchType.EAGER)
    @JoinTable(
        name = "user_roles",
        joinColumns = @JoinColumn(name = "user_id"),
        inverseJoinColumns = @JoinColumn(name = "role_id")
    )
    private Set<Role> roles = new HashSet<>();

    @Column(nullable = false)
    private Boolean enabled = true;

    @Column(nullable = false)
    private Boolean accountNonExpired = true;

    @Column(nullable = false)
    private Boolean accountNonLocked = true;

    @Column(nullable = false)
    private Boolean credentialsNonExpired = true;

    @Column(length = 255)
    private String profileImageUrl;

    @Column(length = 500)
    private String bio;

    @Column(length = 100)
    private String providerType; // LOCAL, GOOGLE, GITHUB

    @Column(length = 255)
    private String providerId; // ID externo si es OAuth
}
```

---

## 3. DTOs (Data Transfer Objects)

### 3.1 LoginRequest.java

```java
package com.hospitalfamilia.auth.dto;

import jakarta.validation.constraints.Email;
import jakarta.validation.constraints.NotBlank;
import lombok.AllArgsConstructor;
import lombok.Getter;
import lombok.NoArgsConstructor;
import lombok.Setter;

@Getter
@Setter
@NoArgsConstructor
@AllArgsConstructor
public class LoginRequest {

    @NotBlank(message = "El email es requerido")
    @Email(message = "Formato de email inválido")
    private String email;

    @NotBlank(message = "La contraseña es requerida")
    private String password;
}
```

### 3.2 LoginResponse.java

```java
package com.hospitalfamilia.auth.dto;

import lombok.AllArgsConstructor;
import lombok.Getter;
import lombok.NoArgsConstructor;
import lombok.Setter;

@Getter
@Setter
@NoArgsConstructor
@AllArgsConstructor
public class LoginResponse {

    private String accessToken;
    private String refreshToken;
    private String tokenType = "Bearer";
    private Long expiresIn; // en milisegundos
    private UserDTO user;
}
```

### 3.3 RegisterRequest.java

```java
package com.hospitalfamilia.auth.dto;

import jakarta.validation.constraints.*;
import lombok.AllArgsConstructor;
import lombok.Getter;
import lombok.NoArgsConstructor;
import lombok.Setter;

@Getter
@Setter
@NoArgsConstructor
@AllArgsConstructor
public class RegisterRequest {

    @NotBlank(message = "El email es requerido")
    @Email(message = "Formato de email inválido")
    private String email;

    @NotBlank(message = "El nombre de usuario es requerido")
    @Size(min = 3, max = 50, message = "El nombre de usuario debe tener entre 3 y 50 caracteres")
    private String username;

    @NotBlank(message = "La contraseña es requerida")
    @Size(min = 8, message = "La contraseña debe tener al menos 8 caracteres")
    private String password;

    @NotBlank(message = "Confirmar contraseña es requerido")
    private String confirmPassword;

    @NotBlank(message = "El nombre es requerido")
    private String firstName;

    @NotBlank(message = "El apellido es requerido")
    private String lastName;

    @Pattern(regexp = "^\\+?[1-9]\\d{1,14}$", message = "Formato de teléfono inválido")
    private String phoneNumber;

    @NotBlank(message = "El rol es requerido")
    private String roleName; // TUTOR, PACIENTE, DOCTOR
}
```

### 3.4 UserDTO.java

```java
package com.hospitalfamilia.auth.dto;

import lombok.AllArgsConstructor;
import lombok.Getter;
import lombok.NoArgsConstructor;
import lombok.Setter;
import java.util.Set;

@Getter
@Setter
@NoArgsConstructor
@AllArgsConstructor
public class UserDTO {

    private Long id;
    private String username;
    private String email;
    private String firstName;
    private String lastName;
    private String phoneNumber;
    private String profileImageUrl;
    private String bio;
    private Boolean enabled;
    private Set<String> roles; // nombres de roles
    private Set<String> permissions; // códigos de permisos
}
```

### 3.5 ApiResponse.java

```java
package com.hospitalfamilia.common.dto;

import lombok.AllArgsConstructor;
import lombok.Getter;
import lombok.NoArgsConstructor;
import lombok.Setter;
import java.time.LocalDateTime;

@Getter
@Setter
@NoArgsConstructor
@AllArgsConstructor
public class ApiResponse<T> {

    private Boolean success;
    private String message;
    private T data;
    private LocalDateTime timestamp = LocalDateTime.now();
    private String path;
    private Integer statusCode;

    public ApiResponse(Boolean success, String message, T data) {
        this.success = success;
        this.message = message;
        this.data = data;
    }

    public static <T> ApiResponse<T> success(String message, T data) {
        return new ApiResponse<>(true, message, data);
    }

    public static <T> ApiResponse<T> error(String message, T data) {
        return new ApiResponse<>(false, message, data);
    }
}
```

---

## 4. Configuración de Seguridad

### 4.1 SecurityConfig.java

```java
package com.hospitalfamilia.config;

import com.hospitalfamilia.auth.security.JwtAuthenticationFilter;
import com.hospitalfamilia.auth.security.JwtAuthenticationEntryPoint;
import com.hospitalfamilia.auth.security.CustomAccessDeniedHandler;
import com.hospitalfamilia.auth.service.UserDetailsServiceImpl;
import lombok.RequiredArgsConstructor;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.authentication.AuthenticationManager;
import org.springframework.security.authentication.dao.DaoAuthenticationProvider;
import org.springframework.security.config.annotation.authentication.configuration.AuthenticationConfiguration;
import org.springframework.security.config.annotation.method.configuration.EnableMethodSecurity;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.http.SessionCreationPolicy;
import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;
import org.springframework.security.crypto.password.PasswordEncoder;
import org.springframework.security.web.SecurityFilterChain;
import org.springframework.security.web.authentication.UsernamePasswordAuthenticationFilter;

@Configuration
@EnableMethodSecurity(prePostEnabled = true)
@RequiredArgsConstructor
public class SecurityConfig {

    private final UserDetailsServiceImpl userDetailsService;
    private final JwtAuthenticationEntryPoint jwtAuthenticationEntryPoint;
    private final CustomAccessDeniedHandler customAccessDeniedHandler;
    private final JwtAuthenticationFilter jwtAuthenticationFilter;

    @Bean
    public PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }

    @Bean
    public DaoAuthenticationProvider authenticationProvider() {
        DaoAuthenticationProvider authProvider = new DaoAuthenticationProvider();
        authProvider.setUserDetailsService(userDetailsService);
        authProvider.setPasswordEncoder(passwordEncoder());
        return authProvider;
    }

    @Bean
    public AuthenticationManager authenticationManager(AuthenticationConfiguration authConfig) throws Exception {
        return authConfig.getAuthenticationManager();
    }

    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http
            .csrf(csrf -> csrf.disable())
            .sessionManagement(session -> session.sessionCreationPolicy(SessionCreationPolicy.STATELESS))
            .authorizeHttpRequests(authz -> authz
                .requestMatchers("/api/auth/login", "/api/auth/register", "/api/auth/refresh").permitAll()
                .requestMatchers("/swagger-ui/**", "/v3/api-docs/**", "/swagger-resources/**").permitAll()
                .requestMatchers("/actuator/**").permitAll()
                .anyRequest().authenticated()
            )
            .exceptionHandling(exception -> exception
                .authenticationEntryPoint(jwtAuthenticationEntryPoint)
                .accessDeniedHandler(customAccessDeniedHandler)
            )
            .addFilterBefore(jwtAuthenticationFilter, UsernamePasswordAuthenticationFilter.class);

        return http.build();
    }
}
```

### 4.2 JwtTokenProvider.java

```java
package com.hospitalfamilia.auth.service;

import io.jsonwebtoken.*;
import io.jsonwebtoken.security.Keys;
import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.security.core.Authentication;
import org.springframework.stereotype.Component;

import javax.crypto.SecretKey;
import java.util.Date;
import java.util.stream.Collectors;

@Component
@Slf4j
public class JwtTokenProvider {

    @Value("${app.jwt.secret:mySecretKeyForHospitalFamiliaApplicationWithMinimum256BitsForHS256Algorithm}")
    private String jwtSecret;

    @Value("${app.jwt.expiration:86400000}") // 24 horas por defecto
    private Long jwtExpiration;

    @Value("${app.jwt.refresh-expiration:604800000}") // 7 días por defecto
    private Long jwtRefreshExpiration;

    private SecretKey getSigningKey() {
        return Keys.hmacShaKeyFor(jwtSecret.getBytes());
    }

    public String generateToken(Authentication authentication) {
        String username = authentication.getName();
        String roles = authentication.getAuthorities().stream()
            .map(auth -> auth.getAuthority())
            .collect(Collectors.joining(","));

        Date now = new Date();
        Date expiryDate = new Date(now.getTime() + jwtExpiration);

        return Jwts.builder()
            .subject(username)
            .claim("roles", roles)
            .issuedAt(now)
            .expiration(expiryDate)
            .signWith(getSigningKey(), SignatureAlgorithm.HS256)
            .compact();
    }

    public String generateTokenFromUsername(String username, String roles) {
        Date now = new Date();
        Date expiryDate = new Date(now.getTime() + jwtExpiration);

        return Jwts.builder()
            .subject(username)
            .claim("roles", roles)
            .issuedAt(now)
            .expiration(expiryDate)
            .signWith(getSigningKey(), SignatureAlgorithm.HS256)
            .compact();
    }

    public String generateRefreshToken(String username) {
        Date now = new Date();
        Date expiryDate = new Date(now.getTime() + jwtRefreshExpiration);

        return Jwts.builder()
            .subject(username)
            .issuedAt(now)
            .expiration(expiryDate)
            .signWith(getSigningKey(), SignatureAlgorithm.HS256)
            .compact();
    }

    public String getUsernameFromToken(String token) {
        Claims claims = Jwts.parserBuilder()
            .setSigningKey(getSigningKey())
            .build()
            .parseClaimsJws(token)
            .getBody();
        return claims.getSubject();
    }

    public boolean validateToken(String token) {
        try {
            Jwts.parserBuilder()
                .setSigningKey(getSigningKey())
                .build()
                .parseClaimsJws(token);
            return true;
        } catch (SecurityException ex) {
            log.error("Invalid JWT signature: {}", ex.getMessage());
        } catch (MalformedJwtException ex) {
            log.error("Invalid JWT token: {}", ex.getMessage());
        } catch (ExpiredJwtException ex) {
            log.error("Expired JWT token: {}", ex.getMessage());
        } catch (UnsupportedJwtException ex) {
            log.error("Unsupported JWT token: {}", ex.getMessage());
        } catch (IllegalArgumentException ex) {
            log.error("JWT claims string is empty: {}", ex.getMessage());
        }
        return false;
    }

    public boolean isTokenExpired(String token) {
        try {
            Claims claims = Jwts.parserBuilder()
                .setSigningKey(getSigningKey())
                .build()
                .parseClaimsJws(token)
                .getBody();
            return claims.getExpiration().before(new Date());
        } catch (ExpiredJwtException ex) {
            return true;
        }
    }
}
```

### 4.3 JwtAuthenticationFilter.java

```java
package com.hospitalfamilia.auth.security;

import com.hospitalfamilia.auth.service.JwtTokenProvider;
import lombok.RequiredArgsConstructor;
import lombok.extern.slf4j.Slf4j;
import org.springframework.security.authentication.UsernamePasswordAuthenticationToken;
import org.springframework.security.core.context.SecurityContextHolder;
import org.springframework.security.core.userdetails.UserDetails;
import org.springframework.security.core.userdetails.UserDetailsService;
import org.springframework.security.web.authentication.WebAuthenticationDetailsSource;
import org.springframework.stereotype.Component;
import org.springframework.util.StringUtils;
import org.springframework.web.filter.OncePerRequestFilter;

import jakarta.servlet.FilterChain;
import jakarta.servlet.ServletException;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;
import java.io.IOException;

@Component
@RequiredArgsConstructor
@Slf4j
public class JwtAuthenticationFilter extends OncePerRequestFilter {

    private final JwtTokenProvider jwtTokenProvider;
    private final UserDetailsService userDetailsService;

    @Override
    protected void doFilterInternal(HttpServletRequest request, HttpServletResponse response,
                                    FilterChain filterChain) throws ServletException, IOException {
        try {
            String jwt = getJwtFromRequest(request);

            if (StringUtils.hasText(jwt) && jwtTokenProvider.validateToken(jwt)) {
                String username = jwtTokenProvider.getUsernameFromToken(jwt);
                UserDetails userDetails = userDetailsService.loadUserByUsername(username);

                UsernamePasswordAuthenticationToken authentication =
                    new UsernamePasswordAuthenticationToken(
                        userDetails, null, userDetails.getAuthorities());
                authentication.setDetails(
                    new WebAuthenticationDetailsSource().buildDetails(request));

                SecurityContextHolder.getContext().setAuthentication(authentication);
            }
        } catch (Exception ex) {
            log.error("Could not set user authentication in security context", ex);
        }

        filterChain.doFilter(request, response);
    }

    private String getJwtFromRequest(HttpServletRequest request) {
        String bearerToken = request.getHeader("Authorization");
        if (StringUtils.hasText(bearerToken) && bearerToken.startsWith("Bearer ")) {
            return bearerToken.substring(7);
        }
        return null;
    }
}
```

---

## 5. Servicios

### 5.1 AuthService.java

```java
package com.hospitalfamilia.auth.service;

import com.hospitalfamilia.auth.dto.*;
import com.hospitalfamilia.auth.entity.Role;
import com.hospitalfamilia.auth.entity.User;
import com.hospitalfamilia.auth.exception.AuthenticationException;
import com.hospitalfamilia.auth.exception.UserAlreadyExistsException;
import com.hospitalfamilia.auth.repository.RoleRepository;
import com.hospitalfamilia.auth.repository.UserRepository;
import lombok.RequiredArgsConstructor;
import lombok.extern.slf4j.Slf4j;
import org.springframework.security.authentication.AuthenticationManager;
import org.springframework.security.authentication.UsernamePasswordAuthenticationToken;
import org.springframework.security.core.Authentication;
import org.springframework.security.crypto.password.PasswordEncoder;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

@Service
@RequiredArgsConstructor
@Slf4j
public class AuthService {

    private final AuthenticationManager authenticationManager;
    private final JwtTokenProvider jwtTokenProvider;
    private final UserRepository userRepository;
    private final RoleRepository roleRepository;
    private final PasswordEncoder passwordEncoder;

    @Transactional
    public LoginResponse login(LoginRequest request) {
        try {
            Authentication authentication = authenticationManager.authenticate(
                new UsernamePasswordAuthenticationToken(
                    request.getEmail(),
                    request.getPassword()
                )
            );

            String accessToken = jwtTokenProvider.generateToken(authentication);
            String refreshToken = jwtTokenProvider.generateRefreshToken(request.getEmail());

            User user = userRepository.findByEmail(request.getEmail())
                .orElseThrow(() -> new AuthenticationException("Usuario no encontrado"));

            UserDTO userDTO = mapToUserDTO(user);

            return new LoginResponse(
                accessToken,
                refreshToken,
                "Bearer",
                jwtTokenProvider.getJwtExpiration(),
                userDTO
            );
        } catch (Exception ex) {
            log.error("Error during login: {}", ex.getMessage());
            throw new AuthenticationException("Credenciales inválidas");
        }
    }

    @Transactional
    public UserDTO register(RegisterRequest request) {
        // Validar que las contraseñas coincidan
        if (!request.getPassword().equals(request.getConfirmPassword())) {
            throw new AuthenticationException("Las contraseñas no coinciden");
        }

        // Verificar que el usuario no exista
        if (userRepository.existsByEmail(request.getEmail())) {
            throw new UserAlreadyExistsException("El email ya está registrado");
        }

        if (userRepository.existsByUsername(request.getUsername())) {
            throw new UserAlreadyExistsException("El nombre de usuario ya está registrado");
        }

        // Crear nuevo usuario
        User user = new User();
        user.setEmail(request.getEmail());
        user.setUsername(request.getUsername());
        user.setPassword(passwordEncoder.encode(request.getPassword()));
        user.setFirstName(request.getFirstName());
        user.setLastName(request.getLastName());
        user.setPhoneNumber(request.getPhoneNumber());
        user.setEnabled(true);

        // Asignar rol
        Role role = roleRepository.findByName(request.getRoleName())
            .orElseThrow(() -> new AuthenticationException("Rol no válido: " + request.getRoleName()));
        user.getRoles().add(role);

        User savedUser = userRepository.save(user);
        log.info("Nuevo usuario registrado: {}", savedUser.getUsername());

        return mapToUserDTO(savedUser);
    }

    public LoginResponse refreshToken(TokenRefreshRequest request) {
        String refreshToken = request.getRefreshToken();

        if (!jwtTokenProvider.validateToken(refreshToken)) {
            throw new AuthenticationException("Token de refresco inválido o expirado");
        }

        String username = jwtTokenProvider.getUsernameFromToken(refreshToken);
        User user = userRepository.findByUsername(username)
            .orElseThrow(() -> new AuthenticationException("Usuario no encontrado"));

        String roles = user.getRoles().stream()
            .map(Role::getName)
            .reduce((r1, r2) -> r1 + "," + r2)
            .orElse("");

        String newAccessToken = jwtTokenProvider.generateTokenFromUsername(username, roles);

        return new LoginResponse(
            newAccessToken,
            refreshToken,
            "Bearer",
            jwtTokenProvider.getJwtExpiration(),
            mapToUserDTO(user)
        );
    }

    private UserDTO mapToUserDTO(User user) {
        UserDTO dto = new UserDTO();
        dto.setId(user.getId());
        dto.setUsername(user.getUsername());
        dto.setEmail(user.getEmail());
        dto.setFirstName(user.getFirstName());
        dto.setLastName(user.getLastName());
        dto.setPhoneNumber(user.getPhoneNumber());
        dto.setProfileImageUrl(user.getProfileImageUrl());
        dto.setBio(user.getBio());
        dto.setEnabled(user.getEnabled());
        dto.setRoles(
            user.getRoles().stream()
                .map(Role::getName)
                .collect(java.util.stream.Collectors.toSet())
        );
        dto.setPermissions(
            user.getRoles().stream()
                .flatMap(role -> role.getPermissions().stream())
                .map(permission -> permission.getCode())
                .collect(java.util.stream.Collectors.toSet())
        );
        return dto;
    }
}
```

### 5.2 UserDetailsServiceImpl.java

```java
package com.hospitalfamilia.auth.service;

import com.hospitalfamilia.auth.entity.User;
import com.hospitalfamilia.auth.repository.UserRepository;
import lombok.RequiredArgsConstructor;
import org.springframework.security.core.GrantedAuthority;
import org.springframework.security.core.authority.SimpleGrantedAuthority;
import org.springframework.security.core.userdetails.UserDetails;
import org.springframework.security.core.userdetails.UserDetailsService;
import org.springframework.security.core.userdetails.UsernameNotFoundException;
import org.springframework.stereotype.Service;

import java.util.Collection;
import java.util.stream.Collectors;

@Service
@RequiredArgsConstructor
public class UserDetailsServiceImpl implements UserDetailsService {

    private final UserRepository userRepository;

    @Override
    public UserDetails loadUserByUsername(String email) throws UsernameNotFoundException {
        User user = userRepository.findByEmail(email)
            .orElseThrow(() -> new UsernameNotFoundException("Usuario no encontrado: " + email));

        return org.springframework.security.core.userdetails.User.builder()
            .username(user.getEmail())
            .password(user.getPassword())
            .authorities(getAuthorities(user))
            .accountExpired(!user.getAccountNonExpired())
            .accountLocked(!user.getAccountNonLocked())
            .credentialsExpired(!user.getCredentialsNonExpired())
            .disabled(!user.getEnabled())
            .build();
    }

    private Collection<? extends GrantedAuthority> getAuthorities(User user) {
        return user.getRoles().stream()
            .flatMap(role -> role.getPermissions().stream()
                .map(permission -> new SimpleGrantedAuthority("ROLE_" + role.getName())))
            .collect(Collectors.toSet());
    }
}
```

---

## 6. Controladores

### 6.1 AuthController.java

```java
package com.hospitalfamilia.auth.controller;

import com.hospitalfamilia.auth.dto.*;
import com.hospitalfamilia.auth.service.AuthService;
import com.hospitalfamilia.common.dto.ApiResponse;
import jakarta.validation.Valid;
import lombok.RequiredArgsConstructor;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping("/api/auth")
@RequiredArgsConstructor
@CrossOrigin(origins = "*", maxAge = 3600)
public class AuthController {

    private final AuthService authService;

    @PostMapping("/login")
    public ResponseEntity<ApiResponse<LoginResponse>> login(@Valid @RequestBody LoginRequest request) {
        LoginResponse response = authService.login(request);
        return ResponseEntity.ok(ApiResponse.success("Login exitoso", response));
    }

    @PostMapping("/register")
    public ResponseEntity<ApiResponse<UserDTO>> register(@Valid @RequestBody RegisterRequest request) {
        UserDTO userDTO = authService.register(request);
        return ResponseEntity.status(HttpStatus.CREATED)
            .body(ApiResponse.success("Usuario registrado exitosamente", userDTO));
    }

    @PostMapping("/refresh")
    public ResponseEntity<ApiResponse<LoginResponse>> refreshToken(@Valid @RequestBody TokenRefreshRequest request) {
        LoginResponse response = authService.refreshToken(request);
        return ResponseEntity.ok(ApiResponse.success("Token refrescado", response));
    }

    @GetMapping("/validate")
    public ResponseEntity<ApiResponse<Boolean>> validateToken() {
        return ResponseEntity.ok(ApiResponse.success("Token válido", true));
    }

    @PostMapping("/logout")
    public ResponseEntity<ApiResponse<String>> logout() {
        // En JWT, el logout es manejado en el cliente eliminando el token
        return ResponseEntity.ok(ApiResponse.success("Sesión cerrada correctamente", "OK"));
    }
}
```

---

## 7. Manejo de Excepciones

### 7.1 Excepciones Personalizadas

```java
// AuthenticationException.java
package com.hospitalfamilia.auth.exception;
public class AuthenticationException extends RuntimeException {
    public AuthenticationException(String message) {
        super(message);
    }
}

// UserAlreadyExistsException.java
package com.hospitalfamilia.auth.exception;
public class UserAlreadyExistsException extends RuntimeException {
    public UserAlreadyExistsException(String message) {
        super(message);
    }
}

// TokenException.java
package com.hospitalfamilia.auth.exception;
public class TokenException extends RuntimeException {
    public TokenException(String message) {
        super(message);
    }
}
```

### 7.2 GlobalExceptionHandler.java

```java
package com.hospitalfamilia.common.exception;

import com.hospitalfamilia.auth.exception.AuthenticationException;
import com.hospitalfamilia.auth.exception.UserAlreadyExistsException;
import com.hospitalfamilia.common.dto.ApiResponse;
import lombok.extern.slf4j.Slf4j;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.MethodArgumentNotValidException;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.RestControllerAdvice;
import org.springframework.web.servlet.mvc.method.annotation.ResponseEntityExceptionHandler;

@RestControllerAdvice
@Slf4j
public class GlobalExceptionHandler extends ResponseEntityExceptionHandler {

    @ExceptionHandler(AuthenticationException.class)
    public ResponseEntity<ApiResponse<String>> handleAuthenticationException(AuthenticationException ex) {
        log.error("Authentication error: {}", ex.getMessage());
        return ResponseEntity.status(HttpStatus.UNAUTHORIZED)
            .body(ApiResponse.error(ex.getMessage(), null));
    }

    @ExceptionHandler(UserAlreadyExistsException.class)
    public ResponseEntity<ApiResponse<String>> handleUserAlreadyExistsException(UserAlreadyExistsException ex) {
        log.warn("User already exists: {}", ex.getMessage());
        return ResponseEntity.status(HttpStatus.CONFLICT)
            .body(ApiResponse.error(ex.getMessage(), null));
    }

    @ExceptionHandler(MethodArgumentNotValidException.class)
    public ResponseEntity<ApiResponse<String>> handleValidationException(MethodArgumentNotValidException ex) {
        String message = ex.getBindingResult().getFieldErrors().stream()
            .map(error -> error.getField() + ": " + error.getDefaultMessage())
            .findFirst()
            .orElse("Validación fallida");
        return ResponseEntity.status(HttpStatus.BAD_REQUEST)
            .body(ApiResponse.error(message, null));
    }

    @ExceptionHandler(Exception.class)
    public ResponseEntity<ApiResponse<String>> handleGenericException(Exception ex) {
        log.error("Unexpected error: {}", ex.getMessage(), ex);
        return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR)
            .body(ApiResponse.error("Error interno del servidor", null));
    }
}
```

---

## 8. Configuración de Propiedades

### 8.1 application.properties

```properties
# Server
server.port=8080

# Database
spring.datasource.url=jdbc:postgresql://localhost:5432/hospital_familia
spring.datasource.username=postgres
spring.datasource.password=postgres
spring.jpa.database-platform=org.hibernate.dialect.PostgreSQLDialect
spring.jpa.hibernate.ddl-auto=validate
spring.jpa.show-sql=false
spring.jpa.properties.hibernate.format_sql=true

# JWT
app.jwt.secret=mySecretKeyForHospitalFamiliaApplicationWithMinimum256BitsForHS256Algorithm
app.jwt.expiration=86400000
app.jwt.refresh-expiration=604800000

# Logging
logging.level.root=INFO
logging.level.com.hospitalfamilia=DEBUG

# Swagger/OpenAPI
springdoc.api-docs.path=/v3/api-docs
springdoc.swagger-ui.path=/swagger-ui.html
springdoc.swagger-ui.enabled=true

# Actuator
management.endpoints.web.exposure.include=health,info,metrics
management.endpoint.health.show-details=when-authorized
```

---

## 9. Migraciones de Base de Datos (Flyway)

### 9.1 V1\_\_Initial_Schema.sql

```sql
-- Tabla de permisos
CREATE TABLE permissions (
    id BIGSERIAL PRIMARY KEY,
    code VARCHAR(100) NOT NULL UNIQUE,
    description VARCHAR(255),
    resource VARCHAR(100) NOT NULL,
    action VARCHAR(50) NOT NULL,
    created_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
    created_by VARCHAR(50),
    updated_by VARCHAR(50)
);

-- Tabla de roles
CREATE TABLE roles (
    id BIGSERIAL PRIMARY KEY,
    name VARCHAR(50) NOT NULL UNIQUE,
    description VARCHAR(255),
    active BOOLEAN NOT NULL DEFAULT TRUE,
    created_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
    created_by VARCHAR(50),
    updated_by VARCHAR(50)
);

-- Tabla de usuarios
CREATE TABLE users (
    id BIGSERIAL PRIMARY KEY,
    username VARCHAR(50) NOT NULL UNIQUE,
    email VARCHAR(120) NOT NULL UNIQUE,
    password VARCHAR(255) NOT NULL,
    first_name VARCHAR(100),
    last_name VARCHAR(100),
    phone_number VARCHAR(20),
    profile_image_url VARCHAR(255),
    bio VARCHAR(500),
    provider_type VARCHAR(100),
    provider_id VARCHAR(255),
    enabled BOOLEAN NOT NULL DEFAULT TRUE,
    account_non_expired BOOLEAN NOT NULL DEFAULT TRUE,
    account_non_locked BOOLEAN NOT NULL DEFAULT TRUE,
    credentials_non_expired BOOLEAN NOT NULL DEFAULT TRUE,
    created_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
    created_by VARCHAR(50),
    updated_by VARCHAR(50)
);

-- Tabla de relación usuario-roles
CREATE TABLE user_roles (
    user_id BIGINT NOT NULL,
    role_id BIGINT NOT NULL,
    PRIMARY KEY (user_id, role_id),
    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE,
    FOREIGN KEY (role_id) REFERENCES roles(id) ON DELETE CASCADE
);

-- Tabla de relación roles-permisos
CREATE TABLE role_permissions (
    role_id BIGINT NOT NULL,
    permission_id BIGINT NOT NULL,
    PRIMARY KEY (role_id, permission_id),
    FOREIGN KEY (role_id) REFERENCES roles(id) ON DELETE CASCADE,
    FOREIGN KEY (permission_id) REFERENCES permissions(id) ON DELETE CASCADE
);

-- Índices
CREATE INDEX idx_users_email ON users(email);
CREATE INDEX idx_users_username ON users(username);
CREATE INDEX idx_roles_name ON roles(name);
CREATE INDEX idx_permissions_code ON permissions(code);
```

### 9.2 V2\_\_Initial_Data.sql

```sql
-- Permisos base
INSERT INTO permissions (code, description, resource, action) VALUES
('READ_PATIENT', 'Leer información del paciente', 'PATIENT', 'READ'),
('WRITE_PATIENT', 'Crear/editar información del paciente', 'PATIENT', 'WRITE'),
('DELETE_PATIENT', 'Eliminar paciente', 'PATIENT', 'DELETE'),
('READ_EVENT', 'Leer eventos médicos', 'EVENT', 'READ'),
('WRITE_EVENT', 'Crear/editar eventos', 'EVENT', 'WRITE'),
('READ_USER', 'Leer usuarios', 'USER', 'READ'),
('WRITE_USER', 'Crear/editar usuarios', 'USER', 'WRITE'),
('DELETE_USER', 'Eliminar usuario', 'USER', 'DELETE'),
('READ_REPORT', 'Leer reportes', 'REPORT', 'READ'),
('WRITE_NOTIFICATION', 'Enviar notificaciones', 'NOTIFICATION', 'WRITE');

-- Roles base
INSERT INTO roles (name, description, active) VALUES
('TUTOR', 'Tutor legal del paciente', TRUE),
('PACIENTE', 'Paciente bajo cuidado', TRUE),
('DOCTOR', 'Médico profesional', TRUE),
('ADMIN', 'Administrador del sistema', TRUE);

-- Asignar permisos a TUTOR
INSERT INTO role_permissions (role_id, permission_id)
SELECT r.id, p.id FROM roles r, permissions p
WHERE r.name = 'TUTOR' AND p.code IN (
    'READ_PATIENT', 'WRITE_PATIENT', 'READ_EVENT', 'READ_REPORT'
);

-- Asignar permisos a PACIENTE
INSERT INTO role_permissions (role_id, permission_id)
SELECT r.id, p.id FROM roles r, permissions p
WHERE r.name = 'PACIENTE' AND p.code IN (
    'READ_PATIENT', 'READ_EVENT', 'READ_REPORT'
);

-- Asignar permisos a DOCTOR
INSERT INTO role_permissions (role_id, permission_id)
SELECT r.id, p.id FROM roles r, permissions p
WHERE r.name = 'DOCTOR' AND p.code IN (
    'READ_PATIENT', 'WRITE_PATIENT', 'READ_EVENT', 'WRITE_EVENT', 'READ_REPORT', 'WRITE_NOTIFICATION'
);

-- Asignar todos los permisos a ADMIN
INSERT INTO role_permissions (role_id, permission_id)
SELECT r.id, p.id FROM roles r, permissions p
WHERE r.name = 'ADMIN';
```

---

## 10. Tests Unitarios Base

### 10.1 AuthServiceTest.java

```java
package com.hospitalfamilia.auth.service;

import com.hospitalfamilia.auth.dto.LoginRequest;
import com.hospitalfamilia.auth.dto.LoginResponse;
import com.hospitalfamilia.auth.dto.RegisterRequest;
import com.hospitalfamilia.auth.dto.UserDTO;
import com.hospitalfamilia.auth.entity.Role;
import com.hospitalfamilia.auth.entity.User;
import com.hospitalfamilia.auth.exception.UserAlreadyExistsException;
import com.hospitalfamilia.auth.repository.RoleRepository;
import com.hospitalfamilia.auth.repository.UserRepository;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.junit.jupiter.MockitoExtension;
import org.springframework.security.authentication.AuthenticationManager;
import org.springframework.security.crypto.password.PasswordEncoder;

import java.util.HashSet;
import java.util.Optional;

import static org.junit.jupiter.api.Assertions.*;
import static org.mockito.ArgumentMatchers.any;
import static org.mockito.Mockito.*;

@ExtendWith(MockitoExtension.class)
class AuthServiceTest {

    @Mock
    private AuthenticationManager authenticationManager;

    @Mock
    private JwtTokenProvider jwtTokenProvider;

    @Mock
    private UserRepository userRepository;

    @Mock
    private RoleRepository roleRepository;

    @Mock
    private PasswordEncoder passwordEncoder;

    @InjectMocks
    private AuthService authService;

    private RegisterRequest registerRequest;
    private User testUser;
    private Role testRole;

    @BeforeEach
    void setUp() {
        registerRequest = new RegisterRequest();
        registerRequest.setEmail("test@example.com");
        registerRequest.setUsername("testuser");
        registerRequest.setPassword("password123");
        registerRequest.setConfirmPassword("password123");
        registerRequest.setFirstName("Test");
        registerRequest.setLastName("User");
        registerRequest.setPhoneNumber("+1234567890");
        registerRequest.setRoleName("PACIENTE");

        testRole = new Role();
        testRole.setId(1L);
        testRole.setName("PACIENTE");
        testRole.setPermissions(new HashSet<>());

        testUser = new User();
        testUser.setId(1L);
        testUser.setEmail("test@example.com");
        testUser.setUsername("testuser");
        testUser.setPassword("hashedPassword");
        testUser.setFirstName("Test");
        testUser.setLastName("User");
        testUser.setRoles(new HashSet<>());
        testUser.getRoles().add(testRole);
    }

    @Test
    void testRegisterSuccess() {
        when(userRepository.existsByEmail(anyString())).thenReturn(false);
        when(userRepository.existsByUsername(anyString())).thenReturn(false);
        when(roleRepository.findByName("PACIENTE")).thenReturn(Optional.of(testRole));
        when(passwordEncoder.encode("password123")).thenReturn("hashedPassword");
        when(userRepository.save(any(User.class))).thenReturn(testUser);

        UserDTO result = authService.register(registerRequest);

        assertNotNull(result);
        assertEquals("test@example.com", result.getEmail());
        assertEquals("testuser", result.getUsername());
        verify(userRepository, times(1)).save(any(User.class));
    }

    @Test
    void testRegisterUserAlreadyExists() {
        when(userRepository.existsByEmail("test@example.com")).thenReturn(true);

        assertThrows(UserAlreadyExistsException.class,
            () -> authService.register(registerRequest));
    }

    @Test
    void testRegisterPasswordMismatch() {
        registerRequest.setConfirmPassword("wrongPassword");

        assertThrows(com.hospitalfamilia.auth.exception.AuthenticationException.class,
            () -> authService.register(registerRequest));
    }
}
```

---

## 11. Plan de Implementación

### Semana 1-2: Infraestructura Base

- [ ] Crear estructura de paquetes
- [ ] Configurar Spring Security
- [ ] Implementar JWT (generación, validación, refresco)
- [ ] Crear entidades y DTOs
- [ ] Configurar base de datos (Flyway)

### Semana 2-3: Servicios y Controladores

- [ ] Implementar AuthService
- [ ] Implementar AuthController
- [ ] Implementar manejo global de excepciones
- [ ] Tests unitarios para servicios

### Semana 3-4: Validación y Deploy

- [ ] Tests de integración
- [ ] Validar con Postman/Insomnia
- [ ] Configurar CORS
- [ ] Deploy a ambiente DEV

---

## 12. Testing - Endpoints cURL

```bash
# Registrar usuario
curl -X POST http://localhost:8080/api/auth/register \
  -H "Content-Type: application/json" \
  -d '{
    "email": "tutor@example.com",
    "username": "tutor_user",
    "password": "SecurePass123!",
    "confirmPassword": "SecurePass123!",
    "firstName": "Juan",
    "lastName": "Perez",
    "phoneNumber": "+5699123456",
    "roleName": "TUTOR"
  }'

# Login
curl -X POST http://localhost:8080/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{
    "email": "tutor@example.com",
    "password": "SecurePass123!"
  }'

# Validar token
curl -X GET http://localhost:8080/api/auth/validate \
  -H "Authorization: Bearer <your_access_token>"

# Refresh token
curl -X POST http://localhost:8080/api/auth/refresh \
  -H "Content-Type: application/json" \
  -d '{
    "refreshToken": "<your_refresh_token>"
  }'

# Logout
curl -X POST http://localhost:8080/api/auth/logout \
  -H "Authorization: Bearer <your_access_token>"
```

---

## Checklist de Completitud

- [ ] Todas las clases creadas y compiladas sin errores
- [ ] Base de datos inicializada con Flyway
- [ ] Autenticación JWT funcionando
- [ ] Roles y permisos asignados correctamente
- [ ] Endpoints validados con cURL
- [ ] Tests unitarios pasando
- [ ] Documentación Swagger generada
- [ ] CORS configurado para frontend
- [ ] Commit y push a repositorio
