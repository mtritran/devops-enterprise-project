# Shopnow Project – Microservices E-Commerce Platform

## Overview

**Shopnow** is an e-commerce platform built with a **microservices architecture**, demonstrating a complete online shopping system with user management, product catalog, shopping cart functionality, and centralized authentication.

The system consists of **Spring Boot microservices** managed by Spring Cloud infrastructure and a **React frontend**, deployed using **Docker Compose** for development and testing.

## Project: [Shopnow](../shopnow-project/)

### Architecture Components

- **Frontend (React)**: Customer and admin interface for browsing products, managing cart, and placing orders
- **API Gateway**: Single entry point routing requests to appropriate microservices
- **Discovery Server**: Service registry for microservice discovery and load balancing
- **Config Server**: Centralized configuration management
- **Business Services**: User, Product, and Shopping Cart microservices
- **Authentication**: Keycloak for identity and access management
- **Databases**: PostgreSQL for application data, MySQL for Keycloak

## Frontend Application

### Technology Stack
- **Framework**: React.js with modern hooks and components
- **State Management**: Context API for cart and user state
- **Styling**: CSS modules with responsive design
- **HTTP Client**: Axios for API communication

### Features
- User registration and authentication
- Product catalog browsing with categories
- Shopping cart management
- Order placement and history
- Responsive design for mobile and desktop

## Backend Microservices

### API Gateway
- **Technology**: Spring Cloud Gateway
- **Port**: 5860
- **Purpose**: Routes client requests to appropriate microservices
- **Features**:
  - Request routing and load balancing
  - Authentication integration with Keycloak
  - Rate limiting and request filtering

### Discovery Server (Eureka)
- **Technology**: Spring Cloud Netflix Eureka
- **Port**: 8761
- **Purpose**: Service registry and discovery
- **Features**:
  - Automatic service registration
  - Health checking and failover
  - Load balancing support

### Config Server
- **Technology**: Spring Cloud Config
- **Port**: 5859
- **Purpose**: Centralized configuration management
- **Features**:
  - Environment-specific configurations
  - Dynamic configuration refresh
  - Git-based configuration storage

### Product Service
- **Technology**: Spring Boot + JPA/Hibernate
- **Port**: 5861
- **Database**: PostgreSQL
- **Purpose**: Product catalog management
- **Features**:
  - CRUD operations for products
  - Category management
  - Product search and filtering
  - Inventory tracking

### User Service
- **Technology**: Spring Boot + JPA
- **Port**: 5865
- **Purpose**: User profile management
- **Features**:
  - User registration and profile management
  - Integration with Keycloak for authentication
  - User preferences and settings

### Shopping Cart Service
- **Technology**: Spring Boot + JPA
- **Port**: 5863
- **Purpose**: Shopping cart operations
- **Features**:
  - Add/remove items from cart
  - Quantity management
  - Price calculations
  - Cart persistence

### Authentication Service (Keycloak)
- **Technology**: Keycloak
- **Port**: 8080
- **Purpose**: Identity and access management
- **Features**:
  - User authentication and authorization
  - JWT token management
  - Role-based access control
  - Social login integration

## Infrastructure and Deployment

### Docker Configuration

Each microservice includes:
- **Dockerfile**: Multi-stage build for optimized images
- **Maven wrapper**: For consistent builds across environments
- **Health checks**: Container health monitoring

### Docker Compose Setup

The `docker-compose.yaml` orchestrates:
- All microservices with proper dependencies
- Database containers with volume persistence
- Network configuration for service communication
- Environment variables for configuration

```yaml
# Key services in docker-compose.yaml
services:
  discovery-server:
    build: ./discovery-server
    ports:
      - "8761:8761"
  
  config-server:
    build: ./config-server
    depends_on:
      - discovery-server
    ports:
      - "5859:5859"
  
  # Additional services...
```

### Database Configuration

**PostgreSQL** (Application Database):
- Stores product, user, and shopping cart data
- Initialized with sample data via `product-data.sql`
- Volume mapping for data persistence

**MySQL** (Keycloak Database):
- Dedicated database for Keycloak configuration
- Realm configuration via `keycloak-realms/realm-export.json`

## Service Endpoints

| Service | Port | Endpoint | Purpose |
|---------|------|----------|---------|
| Frontend | 3000 | http://<ip-public-dev-server>:3000 | React UI |
| API Gateway | 5860 | http://<ip-public-dev-server>:5860 | Main API entry |
| Eureka | 8761 | http://<ip-public-dev-server>:8761 | Service registry |
| Config Server | 5859 | http://<ip-public-dev-server>:5859 | Configuration |
| Product Service | 5861 | http://<ip-public-dev-server>:5861 | Product API |
| User Service | 5865 | http://<ip-public-dev-server>:5865 | User API |
| Shopping Cart | 5863 | http://<ip-public-dev-server>:5863 | Cart API |
| Keycloak | 8080 | http://<ip-public-dev-server>:8080 | Authentication |

## Key Features Demonstrated

- **Microservices Architecture**: Loosely coupled, independently deployable services
- **Service Discovery**: Automatic service registration and discovery
- **Centralized Configuration**: Dynamic configuration management
- **API Gateway Pattern**: Single entry point with routing and security
- **Authentication & Authorization**: JWT-based security with Keycloak
- **Database Per Service**: Each microservice owns its data
- **Containerization**: Docker-based deployment for consistency
- **API Documentation**: Swagger integration for all services

## Technology Stack Summary

**Backend**:
- Spring Boot 2.x
- Spring Cloud (Gateway, Config, Eureka)
- JPA/Hibernate
- PostgreSQL, MySQL
- Keycloak
- Maven

**Frontend**:
- React.js
- Axios
- CSS Modules

**Infrastructure**:
- Docker & Docker Compose
- Nginx (Production)
- GCP Compute Engine (Production)