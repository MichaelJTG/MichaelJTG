# 🏢 Resource Hub - Sistema de Gestión de Recursos Empresariales

> **Backend RESTful API con Spring Boot, JWT Authentication y AWS Deployment**

[![Java](https://img.shields.io/badge/Java-17-orange?style=for-the-badge&logo=java)](https://www.oracle.com/java/)
[![Spring Boot](https://img.shields.io/badge/Spring%20Boot-3.2-green?style=for-the-badge&logo=spring)](https://spring.io/projects/spring-boot)
[![MySQL](https://img.shields.io/badge/MySQL-8.0-blue?style=for-the-badge&logo=mysql)](https://www.mysql.com/)
[![AWS](https://img.shields.io/badge/AWS-EC2%20%7C%20RDS-orange?style=for-the-badge&logo=amazon-aws)](https://aws.amazon.com/)
[![License](https://img.shields.io/badge/License-MIT-yellow?style=for-the-badge)](LICENSE)

---

## 📋 Descripción

**Resource Hub** es un sistema backend de nivel producción para la gestión de recursos empresariales (salas, equipos, vehículos) con sistema de reservas inteligente. Desarrollado siguiendo arquitectura empresarial en capas, implementa autenticación stateless JWT, persistencia con JPA/Hibernate, y despliegue en infraestructura cloud AWS.

**🎯 Problema que resuelve:** Optimizar la asignación de recursos compartidos en empresas, evitando conflictos de reservas y proporcionando trazabilidad completa de uso.

---

## ✨ Características Principales

### 🔐 Seguridad Robusta
- **Autenticación JWT Stateless** - Tokens firmados con HMAC-SHA256
- **Spring Security** - Filtros personalizados y cadena de seguridad configurada
- **Encriptación BCrypt** - Contraseñas hasheadas con salt automático
- **Prevención SQL Injection** - Uso de JPA con consultas parametrizadas

### 🏗️ Arquitectura Empresarial
- **Patrón MVC en capas** (Controller → Service → Repository)
- **Separación de responsabilidades** - DTOs vs Entidades JPA
- **Gestión centralizada de excepciones** - GlobalExceptionHandler
- **Validaciones Jakarta Validation** - @NotNull, @Email, @Size

### 💾 Persistencia de Datos
- **Modelo relacional normalizado (3FN)** con MySQL 8.0
- **JPA/Hibernate** - Lazy loading, cache de segundo nivel
- **Transacciones ACID** - Garantía de integridad en operaciones críticas
- **Detección de conflictos** - Validación de reservas solapadas con consultas SQL optimizadas

### 📡 API RESTful Documentada
- **15+ endpoints REST** siguiendo convenciones HTTP
- **OpenAPI 3.0 (Swagger UI)** - Documentación interactiva auto-generada
- **Códigos HTTP semánticos** - 200, 201, 400, 404, 409
- **Versionado de API** - Preparado para evolución sin breaking changes

### ☁️ Despliegue Cloud
- **AWS EC2** - Instancias para el backend
- **AWS RDS MySQL** - Base de datos gestionada
- **AWS S3** - Almacenamiento de archivos estáticos
- **CI/CD con GitHub Actions** - Despliegue automatizado

---

## 🛠️ Stack Tecnológico

### Backend
```
Java 17 LTS
Spring Boot 3.2.x
Spring Data JPA
Spring Security
Hibernate ORM
```

### Base de Datos
```
MySQL 8.0
Flyway (migraciones - planificado)
```

### Cloud & DevOps
```
AWS (EC2, RDS, S3)
Docker
GitHub Actions (CI/CD)
Maven
```

### Testing & Documentación
```
JUnit 5
Mockito
Swagger/OpenAPI 3.0
Postman Collections
```

---

## 🚀 Instalación y Configuración

### Prerequisitos
- Java 17+
- Maven 3.8+
- MySQL 8.0+
- (Opcional) Docker

### 1. Clonar el repositorio
```bash
git clone https://github.com/MichaelJTG/ResourceHub-DAM.git
cd ResourceHub-DAM
```

### 2. Configurar base de datos
Crear base de datos en MySQL:
```sql
CREATE DATABASE resourcehub CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
```

### 3. Configurar variables de entorno
Crear archivo `application.properties` en `src/main/resources/`:
```properties
# Base de datos
spring.datasource.url=jdbc:mysql://localhost:3306/resourcehub
spring.datasource.username=TU_USUARIO
spring.datasource.password=TU_PASSWORD

# JWT
jwt.secret=TU_CLAVE_SECRETA_MINIMO_256_BITS
jwt.expiration=86400000

# Hibernate
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
```

### 4. Compilar y ejecutar
```bash
# Compilar proyecto
mvn clean install

# Ejecutar aplicación
mvn spring-boot:run
```

La aplicación estará disponible en: `http://localhost:8080`

---

## 📚 Documentación API

### Swagger UI (Interfaz interactiva)
```
http://localhost:8080/swagger-ui/index.html
```

### Endpoints principales

#### Autenticación
```http
POST /api/auth/register
POST /api/auth/authenticate
```

#### Usuarios
```http
GET    /api/usuarios
GET    /api/usuarios/{id}
PUT    /api/usuarios/{id}
DELETE /api/usuarios/{id}
```

#### Recursos
```http
GET    /api/recursos
POST   /api/recursos
GET    /api/recursos/{id}
PUT    /api/recursos/{id}
DELETE /api/recursos/{id}
```

#### Reservas
```http
POST   /api/reservas/crear
GET    /api/reservas/mis-reservas
GET    /api/reservas/{id}
PUT    /api/reservas/{id}/cancelar
```

### Ejemplo de petición con JWT
```bash
# 1. Registrar usuario
curl -X POST http://localhost:8080/api/auth/register \
  -H "Content-Type: application/json" \
  -d '{
    "nombre": "Juan Pérez",
    "email": "juan@example.com",
    "password": "Password123!"
  }'

# 2. Autenticar y obtener token
curl -X POST http://localhost:8080/api/auth/authenticate \
  -H "Content-Type: application/json" \
  -d '{
    "email": "juan@example.com",
    "password": "Password123!"
  }'

# 3. Usar token en peticiones protegidas
curl -X GET http://localhost:8080/api/reservas/mis-reservas \
  -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
```

---

## 📊 Modelo de Datos

```
┌─────────────┐         ┌─────────────┐         ┌─────────────┐
│  USUARIO    │         │   RESERVA   │         │   RECURSO   │
├─────────────┤         ├─────────────┤         ├─────────────┤
│ id          │◄───┐    │ id          │    ┌───►│ id          │
│ nombre      │    │    │ usuario_id  │────┘    │ nombre      │
│ email       │    └────│ recurso_id  │         │ tipo        │
│ password    │         │ fecha_inicio│         │ ubicacion   │
│ rol         │         │ fecha_fin   │         │ disponible  │
│ activo      │         │ estado      │         │ capacidad   │
└─────────────┘         └─────────────┘         └─────────────┘
```

### Relaciones
- **Usuario** 1:N **Reserva** - Un usuario puede tener múltiples reservas
- **Recurso** 1:N **Reserva** - Un recurso puede ser reservado múltiples veces

---

## 🔒 Seguridad Implementada

### Flujo de Autenticación JWT

```
┌──────────┐                 ┌──────────┐                 ┌──────────┐
│  Cliente │                 │ Backend  │                 │   DB     │
└────┬─────┘                 └────┬─────┘                 └────┬─────┘
     │                            │                            │
     │ POST /auth/authenticate    │                            │
     │ { email, password }        │                            │
     ├───────────────────────────►│                            │
     │                            │  Verificar password        │
     │                            ├───────────────────────────►│
     │                            │◄───────────────────────────┤
     │                            │  Usuario encontrado        │
     │                            │                            │
     │                            │  Generar JWT               │
     │                            │  (firmado HMAC-256)        │
     │   { token: "eyJhb..." }    │                            │
     │◄───────────────────────────┤                            │
     │                            │                            │
     │ GET /reservas              │                            │
     │ Header: Bearer eyJhb...    │                            │
     ├───────────────────────────►│                            │
     │                            │  Validar JWT               │
     │                            │  Extraer usuario           │
     │                            ├───────────────────────────►│
     │                            │◄───────────────────────────┤
     │   { reservas: [...] }      │  Reservas del usuario      │
     │◄───────────────────────────┤                            │
```

---

## 🎯 Próximas Funcionalidades (Roadmap)

- [ ] **WebSockets** - Notificaciones en tiempo real de cambios en reservas
- [ ] **Integración Python** - Módulo de predicción de uso con Machine Learning
- [ ] **Frontend React** - Interfaz de usuario con calendario interactivo
- [ ] **Accesibilidad WCAG 2.1** - Soporte para lectores de pantalla
- [ ] **Tests unitarios** - Cobertura >80% con JUnit y Mockito
- [ ] **Migraciones Flyway** - Control de versiones de esquema de BD
- [ ] **Cache Redis** - Optimización de consultas frecuentes
- [ ] **Logs centralizados** - ELK Stack (Elasticsearch, Logstash, Kibana)

---

## 📈 Métricas del Proyecto

- **Líneas de código:** ~3,500+ (Java)
- **Endpoints REST:** 15+
- **Tablas de BD:** 3 (normalizadas 3FN)
- **Tiempo de respuesta promedio:** <100ms
- **Cobertura de tests:** 45% (en crecimiento)

---

## 🤝 Contribuciones

Este es un proyecto educativo como parte del Grado Superior en Desarrollo de Aplicaciones Multiplataforma (DAM). Sugerencias y feedback son bienvenidos.

---

## 👨‍💻 Autor

**Michael J. Telleria Guadalajara**

- 🔗 LinkedIn: [michael-j-telleria-guadalajara](https://linkedin.com/in/michael-j-telleria-guadalajara)
- 📧 Email: michael.jt.pro@gmail.com
- 💼 Portfolio: [GitHub/MichaelJTG](https://github.com/MichaelJTG)

---

## 📄 Licencia

Este proyecto está bajo la Licencia MIT - ver el archivo [LICENSE](LICENSE) para más detalles.

---

## 🙏 Agradecimientos

- **UNIR** - Universidad Internacional de La Rioja
- **Spring Framework Team** - Por la excelente documentación
- **AWS Academy** - Por la certificación Cloud Foundations

---

<div align="center">

### ⭐ Si este proyecto te resultó útil, dale una estrella!

**Desarrollado con ❤️ usando Spring Boot y mucho café ☕**

</div>
