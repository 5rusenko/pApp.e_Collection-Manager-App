# Database Schema Outline for "pApp.e" Car Collection Management App

This document outlines the database schema for the AppSheet application designed to manage an extensive car model collection, including car model details, events, user interactions, and a detailed history log.

## 1. Collection Main Table

### Purpose
Stores essential information about each car model in the collection, including identification, characteristics, and current status.

### Columns:
- **ItemID** (Text): Unique identifier for each car model, auto-generated. Serves as the primary key for the `CollectionMain` table.
- **ModelName** (Text): The name of the car model. Used extensively throughout the app for display and search purposes.
- **Manufacturer** (Text): Manufacturer of the car model.
- **Year** (Number): Year of manufacture.
- **Color** (Text): Color of the car model.
- **Quantity** (Number): Number of units available for each model.
- **Status** (Enum): Current status of the model (e.g., Available, Sold Out, Damaged).
- **ItemCondition** (Text): Current condition of the item, updated dynamically based on events.

## 2. Collection Events Table

### Purpose
Logs all significant events related to each car model, such as sales, losses, or damages.

### Columns:
- **EventID** (Text): Unique identifier for each event, auto-generated.
- **ItemID** (Ref): Reference to the `CollectionMain` table, linking each event to a specific car model.
- **EventType** (Enum): Type of event (e.g., Sold, Lost, Damaged).
- **EventDate** (Date): Date when the event occurred.
- **QuantityAffected** (Number): Number of items affected by the event.
- **AdditionalDetails** (Text): Further details based on the event type, stored dynamically.

## 3. Collection History Log Table

### Purpose
Captures a detailed log of all changes made to records in the `CollectionMain` table, including updates and deletions.

### Columns:
- **HistoryID** (Text): Unique identifier for each history record, auto-generated.
- **ItemID** (Ref): Reference to the `CollectionMain` table, ensuring each history record is linked to a specific model.
- **ChangedBy** (Ref): Reference to the `Users` table, identifying the user who made the change.
- **ChangeType** (Enum): Type of change made (e.g., Update, Delete).
- **ChangeDetails** (Text): Detailed description of what was changed, including old and new values.
- **ChangeDate** (DateTime): Timestamp when the change occurred.

## 4. Users Table

### Purpose
Manages user accounts and their associated details, serving as a central repository for user information within the app.

### Columns:
- **UserID** (Text): Unique identifier for each user, auto-generated.
- **Email** (Text): Email address of the user.
- **Role** (Enum): Role of the user in the app (e.g., Admin, Viewer).

## Additional Tables for Event Types

### Sales Details Table
Stores detailed information about sales events.

#### Columns:
- **SaleID** (Text): Unique identifier for the sale, linked to `EventID`.
- **ItemID** (Ref): Reference to the `CollectionMain`.
- **BuyerName** (Text): Name of the buyer.
- **BuyerContact** (Text): Contact information of the buyer.
- **SalePrice** (Number): Price at which the item was sold.

### Damage Details Table
Stores detailed information about damages to items.

#### Columns:
- **DamageID** (Text): Unique identifier for the damage event, linked to `EventID`.
- **ItemID** (Ref): Reference to the `CollectionMain`.
- **DamageType** (Text): Type of damage incurred.
- **RepairCost** (Number): Cost associated with repairing the damage.
- **DamageDescription** (Text): Detailed description of the damage.

## Additional Tables and Functionalities (to be documented in separate files)
- **User Interaction Table**: Records user actions within the app, including edits or views of specific car models.
- **Reports Table**: Manages generated reports based on user queries or scheduled reports for inventory status.

This schema will be expanded with further details in subsequent documentation, focusing on specific functionalities and interactions between tables.
