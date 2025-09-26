# Auto Shield Insurance Management System - Project Documentation

## Table of Contents
1. [Project Overview](#project-overview)
2. [System Architecture](#system-architecture)
3. [Technology Stack](#technology-stack)
4. [Key Features](#key-features)
5. [Database Design](#database-design)
6. [API Endpoints](#api-endpoints)
7. [Frontend Components](#frontend-components)
8. [Security Implementation](#security-implementation)
9. [User Workflows](#user-workflows)
10. [Interview Talking Points](#interview-talking-points)

## Project Overview

**Auto Shield Insurance Management System** is a comprehensive full-stack web application designed to manage auto insurance policies, claims, and customer queries. The system provides role-based access for both customers (users) and administrators, enabling efficient insurance management operations.

### Business Problem Solved
- **Manual Policy Management**: Digitized the entire policy application and management process
- **Claim Processing Delays**: Streamlined claim submission and approval workflow
- **Customer Support**: Integrated query management system for better customer service
- **Administrative Overhead**: Automated policy creation and management for administrators

### Target Users
- **End Customers**: Individuals seeking auto insurance for two-wheelers and four-wheelers
- **Insurance Administrators**: Staff managing policies, claims, and customer queries
- **System Administrators**: Technical staff maintaining the system

## System Architecture

### High-Level Architecture
```
Frontend (React.js) ↔ Backend (Spring Boot) ↔ Database (MySQL)
```

### Architecture Patterns Used
1. **MVC Pattern**: Model-View-Controller separation in Spring Boot
2. **Component-Based Architecture**: React components for UI modularity
3. **RESTful API Design**: Stateless communication between frontend and backend
4. **Repository Pattern**: Data access layer abstraction in Spring Boot
5. **Context Pattern**: React Context for state management

### Deployment Architecture
- **Frontend**: React development server (localhost:3000)
- **Backend**: Spring Boot embedded Tomcat (localhost:8082)
- **Database**: MySQL server (localhost:3306)

## Technology Stack

### Frontend Technologies
- **React.js 19.1.1**: Core UI framework
- **React Router 7.9.1**: Client-side routing and navigation
- **Material-UI 7.3.2**: Component library for consistent UI design
- **Axios 1.12.2**: HTTP client for API communication
- **React Slick 0.31.0**: Carousel component for homepage
- **jsPDF 3.0.2**: PDF generation for policy documents
- **Date-fns 4.1.0**: Date manipulation utilities

### Backend Technologies
- **Spring Boot 3.5.5**: Main application framework
- **Spring Security 6**: Authentication and authorization
- **Spring Data JPA**: Database operations and ORM
- **MySQL Connector**: Database connectivity
- **BCrypt**: Password encryption
- **Jakarta Validation**: Input validation
- **Lombok**: Code generation for boilerplate reduction

### Database
- **MySQL 8.0**: Relational database management system

### Development Tools
- **Maven**: Build automation and dependency management
- **npm**: Frontend package management
- **Git**: Version control system

## Key Features

### User Features
1. **Policy Management**
   - Browse available insurance policies
   - Filter policies by vehicle type (Two-Wheeler/Four-Wheeler)
   - Search policies by name or coverage type
   - View detailed policy information (benefits, terms, coverage)
   - Apply for policies with comprehensive forms
   - Download policy applications as PDF

2. **Claim Management**
   - Submit insurance claims with incident details
   - Upload incident photos (base64 encoding)
   - Track claim status (Pending/Approved/Rejected)
   - View claim history and details

3. **Query Management**
   - Submit support queries
   - Track query responses from administrators
   - View query history and status

4. **Profile Management**
   - View and update personal information
   - Change password securely

### Admin Features
1. **Policy Administration**
   - Create new insurance policies
   - Set premium amounts, coverage limits, and durations
   - Manage policy benefits and terms
   - View all created policies

2. **Claim Processing**
   - Review submitted claims with full details
   - View incident photos and claim information
   - Approve or reject claims
   - Manage claim workflow

3. **Customer Support**
   - View all user queries
   - Respond to customer queries
   - Mark queries as resolved

4. **User Management**
   - View all registered users
   - Monitor user activities

### System Features
1. **Authentication & Authorization**
   - Role-based access control (USER/ADMIN)
   - Session-based authentication
   - Secure password storage with BCrypt
   - Protected routes and API endpoints

2. **Data Management**
   - CRUD operations for all entities
   - Data validation and error handling
   - Database relationships and constraints

3. **User Experience**
   - Responsive design for all devices
   - Professional UI with consistent styling
   - Loading states and error handling
   - Form validation with real-time feedback

## Database Design

### Entity Relationship Diagram
```
Users (1) ←→ (M) UserPolicies
Users (1) ←→ (M) Claims
Users (1) ←→ (M) UserQueries
Policies (1) ←→ (M) Claims
```

### Database Tables

#### Users Table
```sql
- id (Primary Key, Auto Increment)
- name (VARCHAR, NOT NULL)
- email (VARCHAR, UNIQUE, NOT NULL)
- password (VARCHAR, NOT NULL) -- BCrypt encrypted
- role (ENUM: USER, ADMIN)
```

#### Policies Table
```sql
- id (Primary Key, Auto Increment)
- policy_name (VARCHAR, NOT NULL)
- vehicle_type (VARCHAR: Two-Wheeler, Four-Wheeler)
- coverage_type (VARCHAR: Comprehensive, Third-Party, etc.)
- premium_amount (DECIMAL(10,2), NOT NULL)
- coverage_amount (DECIMAL(10,2), NOT NULL)
- duration (INTEGER) -- in months
- description (TEXT)
- benefits (TEXT)
- terms (TEXT)
- is_active (BOOLEAN, DEFAULT TRUE)
- created_by_admin (BIGINT)
- created_at (TIMESTAMP)
- updated_at (TIMESTAMP)
```

#### UserPolicies Table
```sql
- id (Primary Key, Auto Increment)
- user_id (Foreign Key → Users.id)
- type (VARCHAR) -- Vehicle type
- details (TEXT) -- JSON string with application details
```

#### Claims Table
```sql
- id (Primary Key, Auto Increment)
- user_id (Foreign Key → Users.id)
- policy_id (Foreign Key → Policies.id)
- claim_number (VARCHAR, UNIQUE)
- claim_type (VARCHAR: Accident, Theft, Fire, etc.)
- requested_amount (DECIMAL(10,2))
- approved_amount (DECIMAL(10,2))
- deductible (DECIMAL(10,2))
- estimated_amount (DECIMAL(10,2))
- incident_date (TIMESTAMP)
- created_at (TIMESTAMP)
- status (VARCHAR: PENDING, APPROVED, REJECTED)
- payment_status (VARCHAR)
- details (TEXT) -- JSON string with claim details
- image_url (LONGTEXT) -- Base64 encoded image
- description (TEXT)
```

#### UserQueries Table
```sql
- id (Primary Key, Auto Increment)
- user_id (Foreign Key → Users.id)
- query_text (TEXT)
- response (TEXT)
- status (VARCHAR: OPEN, RESOLVED)
```

## API Endpoints

### Authentication Endpoints
- `POST /api/auth/register` - User registration
- `POST /api/auth/login` - User login
- `GET /api/auth/me` - Get current user info
- `POST /api/auth/logout` - User logout

### Policy Endpoints
- `GET /api/policies` - Get all active policies
- `POST /api/policies` - Create new policy (Admin only)
- `POST /api/user-policies` - Apply for policy
- `GET /api/user-policies` - Get user's applied policies

### Claim Endpoints
- `POST /api/claims` - Submit new claim
- `GET /api/claims` - Get user's claims
- `GET /api/claims/all` - Get all claims (Admin only)
- `PUT /api/claims/{id}/status` - Update claim status (Admin only)

### Query Endpoints
- `POST /api/queries` - Submit new query
- `GET /api/queries` - Get user's queries
- `PUT /api/queries/{id}/reply` - Reply to query (Admin only)

### User Management Endpoints
- `GET /api/user/all` - Get all users (Admin only)
- `PUT /api/user/profile` - Update user profile

## Frontend Components

### Component Hierarchy
```
App
├── Layout (Public pages wrapper)
│   ├── HomePage
│   ├── LoginPage
│   └── SignupPage
├── UserDashboard (Protected - USER role)
│   ├── AvailablePolicies
│   ├── AppliedPolicies
│   ├── ApplyClaim
│   ├── TrackClaims
│   ├── UserQueries
│   └── UserProfile
└── AdminDashboard (Protected - ADMIN role)
    ├── CreatePolicy
    ├── ManageClaims
    ├── AdminQueries
    ├── ViewPolicies
    └── AdminProfile
```

### Key Components Explained

#### AuthContext
- Manages global authentication state
- Provides user data to all components
- Handles login/logout operations
- Implements protected route logic

#### AvailablePolicies
- Displays all available insurance policies
- Implements filtering and search functionality
- Handles policy application workflow
- Manages payment simulation

#### HomePage
- Landing page with company information
- Features carousel, services, and contact sections
- Responsive design with modern UI

#### Dashboard Components
- Role-based navigation and content
- Consistent layout and styling
- Real-time data updates

## Security Implementation

### Authentication Security
1. **Password Security**
   - BCrypt hashing with salt
   - Minimum password requirements
   - Secure password storage

2. **Session Management**
   - Server-side session storage
   - Session timeout handling
   - Secure session cookies

3. **Authorization**
   - Role-based access control (RBAC)
   - Protected routes on frontend
   - Secured API endpoints on backend

### Data Security
1. **Input Validation**
   - Frontend form validation
   - Backend data validation using Jakarta Validation
   - SQL injection prevention through JPA

2. **CORS Configuration**
   - Configured allowed origins
   - Secure cross-origin requests

3. **Error Handling**
   - Secure error messages
   - No sensitive data exposure in errors

## User Workflows

### User Registration & Login Flow
1. User visits homepage
2. Clicks "Sign Up" → Registration form
3. Selects role (USER/ADMIN)
4. Fills registration details with validation
5. Account created → Redirected to login
6. Login with credentials → Redirected to appropriate dashboard

### Policy Application Flow (User)
1. User logs in → User Dashboard
2. Navigates to "Available Policies"
3. Browses/filters/searches policies
4. Clicks "Apply Now" → Policy details dialog
5. Fills application form with validation
6. Proceeds to payment → Payment form
7. Completes payment → Application submitted
8. Receives confirmation → Can download PDF

### Claim Submission Flow (User)
1. User navigates to "Apply Claims"
2. Selects policy from dropdown
3. Fills claim details (type, amount, description)
4. Uploads incident photo
5. Submits claim → Claim created with PENDING status
6. Can track claim status in "Track Claims"

### Claim Management Flow (Admin)
1. Admin logs in → Admin Dashboard
2. Navigates to "Manage Claims"
3. Views all submitted claims
4. Clicks "View" on specific claim
5. Reviews claim details and incident photo
6. Approves or rejects claim
7. Status updated → User can see updated status

## Interview Talking Points

### Technical Skills Demonstrated
1. **Full-Stack Development**
   - Frontend: React.js with modern hooks and context
   - Backend: Spring Boot with RESTful APIs
   - Database: MySQL with proper relationships

2. **Security Implementation**
   - Authentication and authorization
   - Password encryption
   - Role-based access control
   - Input validation and sanitization

3. **Database Design**
   - Normalized database structure
   - Foreign key relationships
   - Proper indexing and constraints

4. **API Design**
   - RESTful API principles
   - Proper HTTP status codes
   - Error handling and validation

5. **Frontend Development**
   - Component-based architecture
   - State management with Context API
   - Responsive design
   - Form validation and user experience

### Problem-Solving Approach
1. **Requirements Analysis**
   - Identified key stakeholders (users, admins)
   - Defined core functionalities
   - Planned user workflows

2. **System Design**
   - Chose appropriate technology stack
   - Designed scalable architecture
   - Implemented security best practices

3. **Development Process**
   - Incremental development approach
   - Component-based development
   - API-first development

### Business Value Delivered
1. **Efficiency Improvements**
   - Automated policy application process
   - Streamlined claim management
   - Reduced manual paperwork

2. **User Experience**
   - Intuitive interface design
   - Mobile-responsive application
   - Real-time status updates

3. **Administrative Benefits**
   - Centralized policy management
   - Efficient claim processing
   - Customer query management

### Scalability Considerations
1. **Database Optimization**
   - Proper indexing strategy
   - Query optimization
   - Connection pooling

2. **API Performance**
   - Efficient data transfer
   - Pagination for large datasets
   - Caching strategies

3. **Frontend Optimization**
   - Component lazy loading
   - Image optimization
   - Bundle size optimization

### Future Enhancements
1. **Technical Improvements**
   - Microservices architecture
   - Redis caching
   - Real-time notifications
   - Mobile application

2. **Business Features**
   - Payment gateway integration
   - Document management system
   - Analytics and reporting
   - Multi-language support

---

## Conclusion

The Auto Shield Insurance Management System demonstrates a comprehensive understanding of full-stack web development, incorporating modern technologies, security best practices, and user-centered design. The project showcases the ability to build scalable, maintainable, and secure web applications that solve real-world business problems.

The system successfully addresses the key challenges in insurance management while providing an excellent user experience for both customers and administrators. The technical implementation demonstrates proficiency in React.js, Spring Boot, MySQL, and modern web development practices.