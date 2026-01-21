---
applyTo: '**'
---

# Gino-Gelato Application

## Project Overview
Gino-Gelato is a web application for managing a gelato business.

## Tech Stack

### Frontend
- **React** (latest version)
- **TypeScript** for type-safe development
- **TailwindCSS** for styling

### Backend
- **.NET 10** with Minimal API pattern

## Coding Conventions

### TypeScript/React
- Use functional components with hooks
- Prefer named exports over default exports
- Use TypeScript strict mode
- Use interfaces for object shapes, types for unions/primitives
- Follow React naming conventions: PascalCase for components, camelCase for functions/variables

### TailwindCSS
- Use utility classes directly in JSX
- Extract repeated patterns into component classes when appropriate
- Follow mobile-first responsive design

### .NET Minimal API
- Use record types for DTOs
- Follow RESTful API conventions
- Use dependency injection for services
- Keep endpoints organized in separate extension methods
- Use async/await for all I/O operations

## Project Structure Guidelines
- Frontend code in `/frontend` or `/client` directory
- Backend code in `/backend` or `/api` directory
- Shared types/contracts where applicable

