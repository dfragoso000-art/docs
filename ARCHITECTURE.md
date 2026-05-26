# Architecture Overview

This document provides a visual overview of the EDB Docs system architecture, showing how users interact with the documentation platform, the frontend and backend services, and the supporting infrastructure.

## System Architecture

```mermaid
flowchart LR
  %% Users
  subgraph Users[Users]
    U_Web[Web Browser Users]
    U_Mobile[Mobile Users]
    U_API[API Consumers]
  end

  %% Content & CDN
  subgraph Content[Content Delivery]
    CDN[CDN / Static Assets]
    SearchIndex[(Search Index)]
  end

  %% Frontend
  subgraph Frontend[Frontend Layer]
    WebApp[Next.js / React App]
    StaticGen[Static Generation<br/>MDX Processing]
  end

  %% API & Services
  subgraph Services[Backend Services]
    APIServer[API Server]
    DocService[Documentation Service]
    SearchService[Search Service]
    AuthService[Authentication]
  end

  %% Data Layer
  subgraph Data[Data & Storage]
    DocDB[(Documentation DB)]
    ContentStore[(Content Repository<br/>Git)]
    MetadataDB[(Metadata DB)]
  end

  %% External & Monitoring
  subgraph Infra[Infrastructure & Monitoring]
    Analytics[Analytics & Telemetry]
    Logging[Logging & Monitoring]
    Cache[(Cache Layer<br/>Redis)]
  end

  %% User Connections
  U_Web -->|HTTPS| WebApp
  U_Mobile -->|HTTPS| WebApp
  U_API -->|REST API| APIServer

  %% Frontend Connections
  WebApp -->|serves| CDN
  WebApp -->|queries| APIServer
  WebApp -->|processes| StaticGen

  %% CDN Connections
  CDN -->|cached content| U_Web
  CDN -->|cached content| U_Mobile

  %% Service Connections
  APIServer --> DocService
  APIServer --> SearchService
  APIServer --> AuthService

  DocService --> DocDB
  DocService --> ContentStore
  DocService --> Cache

  SearchService --> SearchIndex
  SearchService --> MetadataDB

  AuthService --> MetadataDB

  %% Monitoring
  APIServer -->|emit metrics| Analytics
  APIServer -->|send logs| Logging
  DocService -->|emit metrics| Analytics
  WebApp -->|track events| Analytics

  %% Cache connections
  SearchService --> Cache
  APIServer --> Cache

  style Users fill:#e1f5ff
  style Frontend fill:#f3e5f5
  style Services fill:#fff3e0
  style Data fill:#f1f8e9
  style Infra fill:#fce4ec
```

## Architecture Components

### Users Layer
- **Web Browser Users**: Access documentation through the web interface
- **Mobile Users**: Access documentation on mobile devices via responsive design
- **API Consumers**: Integrate with documentation through REST APIs

### Content Delivery Layer
- **CDN**: Serves static assets and cached content globally
- **Search Index**: Full-text search capability for documentation

### Frontend Layer
- **Next.js / React App**: Modern single-page application for documentation browsing
- **Static Generation**: MDX processing for converting documentation content

### Backend Services
- **API Server**: Main backend service handling requests
- **Documentation Service**: Manages documentation retrieval and processing
- **Search Service**: Provides full-text search functionality
- **Authentication Service**: Handles user authentication and authorization

### Data Layer
- **Documentation DB**: Stores processed documentation metadata
- **Content Repository**: Git-based storage for documentation source files
- **Metadata DB**: Stores user sessions, preferences, and system metadata

### Infrastructure & Monitoring
- **Analytics & Telemetry**: Tracks user behavior and system performance
- **Logging & Monitoring**: Centralized logging for debugging and monitoring
- **Cache Layer**: Redis-based caching for performance optimization

## Key Technologies

- **Frontend**: React, Next.js, MDX (98.3%)
- **Scripting**: JavaScript (1.2%), Shell (0.1%), Python (0.1%)
- **Configuration**: Nunjucks (0.1%)
- **Utilities**: Perl (0.2%)

## Data Flow

1. **Documentation Access**: Users request documentation via Web/Mobile
2. **API Request**: Frontend queries the API server for content
3. **Service Processing**: Documentation and Search services retrieve content
4. **Data Retrieval**: Services query databases and repository
5. **Caching**: Frequently accessed content is cached
6. **CDN Delivery**: Static assets delivered through global CDN
7. **Monitoring**: Metrics and logs collected for analytics

## Deployment Considerations

- Static content is served through CDN for optimal performance
- Documentation is processed at build time when possible
- Search index is maintained and updated regularly
- Caching strategies optimize for frequently accessed content
- Monitoring and analytics inform performance improvements
