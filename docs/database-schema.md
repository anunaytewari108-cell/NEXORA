# NEXORA — Database Schema

Version: 1.0  
Database: PostgreSQL  
Architecture: Relational Database

## 1. Overview

NEXORA uses PostgreSQL as its primary relational database.

The database stores user accounts, profiles, posts, social interactions, notifications and content reports.

## 2. Database Tables

### 2.1 Users

Purpose: Store user account information.

| Column | Type | Constraint |
|---|---|---|
| id | UUID | Primary Key |
| username | VARCHAR(30) | Unique, Not Null |
| email | VARCHAR(255) | Unique, Not Null |
| password_hash | VARCHAR(255) | Not Null |
| is_active | BOOLEAN | Default TRUE |
| created_at | TIMESTAMPTZ | Not Null |
| updated_at | TIMESTAMPTZ | Not Null |

### 2.2 Profiles

Purpose: Store public user information.

| Column | Type | Constraint |
|---|---|---|
| id | UUID | Primary Key |
| user_id | UUID | Foreign Key, Unique |
| display_name | VARCHAR(100) | Nullable |
| bio | VARCHAR(300) | Nullable |
| avatar_url | TEXT | Nullable |
| website | TEXT | Nullable |

### 2.3 Posts

Purpose: Store user-generated posts and threads.

| Column | Type | Constraint |
|---|---|---|
| id | UUID | Primary Key |
| user_id | UUID | Foreign Key |
| content | VARCHAR(10000) | Not Null |
| parent_post_id | UUID | Nullable, Self Foreign Key |
| created_at | TIMESTAMPTZ | Not Null |
| updated_at | TIMESTAMPTZ | Not Null |

parent_post_id connects a reply or thread entry to its parent post.

### 2.4 Follows

Purpose: Manage following relationships.

| Column | Type | Constraint |
|---|---|---|
| id | UUID | Primary Key |
| follower_id | UUID | Foreign Key |
| following_id | UUID | Foreign Key |
| created_at | TIMESTAMPTZ | Not Null |

Unique constraint: follower_id + following_id.

A user cannot follow the same account twice or follow themselves.

### 2.5 Likes

Purpose: Store post likes.

| Column | Type | Constraint |
|---|---|---|
| id | UUID | Primary Key |
| user_id | UUID | Foreign Key |
| post_id | UUID | Foreign Key |
| created_at | TIMESTAMPTZ | Not Null |

Unique constraint: user_id + post_id.

### 2.6 Comments

Purpose: Store replies to posts.

| Column | Type | Constraint |
|---|---|---|
| id | UUID | Primary Key |
| user_id | UUID | Foreign Key |
| post_id | UUID | Foreign Key |
| content | TEXT | Not Null |
| created_at | TIMESTAMPTZ | Not Null |

### 2.7 Reposts

Purpose: Track reposted content.

| Column | Type | Constraint |
|---|---|---|
| id | UUID | Primary Key |
| user_id | UUID | Foreign Key |
| post_id | UUID | Foreign Key |
| created_at | TIMESTAMPTZ | Not Null |

Unique constraint: user_id + post_id.

### 2.8 Bookmarks

Purpose: Save posts for later.

| Column | Type | Constraint |
|---|---|---|
| id | UUID | Primary Key |
| user_id | UUID | Foreign Key |
| post_id | UUID | Foreign Key |
| created_at | TIMESTAMPTZ | Not Null |

Unique constraint: user_id + post_id.

### 2.9 Notifications

Purpose: Store user notifications.

| Column | Type | Constraint |
|---|---|---|
| id | UUID | Primary Key |
| recipient_id | UUID | Foreign Key |
| actor_id | UUID | Foreign Key |
| notification_type | VARCHAR(30) | Not Null |
| post_id | UUID | Nullable, Foreign Key |
| is_read | BOOLEAN | Default FALSE |
| created_at | TIMESTAMPTZ | Not Null |

### 2.10 Reports

Purpose: Store reports against inappropriate content or accounts.

| Column | Type | Constraint |
|---|---|---|
| id | UUID | Primary Key |
| reporter_id | UUID | Foreign Key |
| reported_user_id | UUID | Nullable, Foreign Key |
| post_id | UUID | Nullable, Foreign Key |
| reason | VARCHAR(100) | Not Null |
| details | TEXT | Nullable |
| status | VARCHAR(20) | Default pending |
| created_at | TIMESTAMPTZ | Not Null |

A report must identify either a reported user or a post.

## 3. Database Relationships

- Users → Profiles: One-to-One
- Users → Posts: One-to-Many
- Users → Follows: One-to-Many
- Users → Likes: One-to-Many
- Posts → Likes: One-to-Many
- Posts → Comments: One-to-Many
- Posts → Reposts: One-to-Many
- Users → Bookmarks: One-to-Many
- Users → Notifications: One-to-Many

## 4. Indexing Strategy

Initial indexes will cover:

- users.username
- users.email
- posts.user_id
- posts.created_at
- posts.parent_post_id
- follows.follower_id
- follows.following_id
- likes.post_id
- comments.post_id
- notifications.recipient_id

Composite indexes will be evaluated based on actual query patterns.

## 5. AI/ML Integration — Future

The database will support future AI capabilities.

Planned capabilities:

- Personalized feed recommendations
- User interest modelling
- Content similarity search
- Semantic search using embeddings
- Spam classification
- Content recommendation analytics

PostgreSQL with pgvector will be evaluated for vector-based search.

## 6. Engineering Principles

- Use UUID identifiers.
- Enforce foreign key constraints.
- Prevent duplicate social interactions.
- Use database transactions for critical operations.
- Store timestamps consistently.
- Never store plaintext passwords.
- Apply indexes based on query patterns.
- Protect private user information.

## 7. Future Improvements

- Media storage integration
- Hashtag management
- User activity analytics
- Advanced recommendation data
- Content moderation records
- Database performance optimization