# CollectionMain Table Schema

## Purpose
The `CollectionMain` table is essential for managing detailed information about each car model in the collection, including specifications, current status, and quantity.

## Columns Configuration

### ItemID
- **Type**: Text
- **Description**: Unique identifier for each car model, automatically generated to ensure uniqueness.
- **Key**: True
- **Editable**: False
- **Show**: False
- **Purpose**: Acts as the primary key for the `CollectionMain` table.

### ModelName
- **Type**: Text
- **Description**: Name of the car model.
- **Editable**: True
- **Show**: True
- **Purpose**: Used throughout the app for display and search purposes.

### Manufacturer
- **Type**: Text
- **Description**: Manufacturer of the car model.
- **Editable**: True
- **Show**: True
- **Purpose**: Helps categorize models by manufacturer for better organization and filtering.

### Year
- **Type**: Number
- **Description**: Year of manufacture of the car model.
- **Editable**: True
- **Show**: True
- **Purpose**: Important for historical context and valuation of the car model.

### Color
- **Type**: Text
- **Description**: Color of the car model.
- **Editable**: True
- **Show**: True
- **Purpose**: Useful for visual identification and sorting.

### Quantity
- **Type**: Number
- **Description**: Number of units available for each model.
- **Editable**: True
- **Show**: True
- **Purpose**: Keeps track of inventory levels for each model.

### Status
- **Type**: Enum
- **Description**: Current status of the model (e.g., Available, Sold Out, Damaged).
- **Options**: ['Available', 'Sold Out', 'Damaged']
- **Editable**: True
- **Show**: True
- **Purpose**: Provides quick insight into the availability and condition of the model.

### ItemCondition
- **Type**: Text
- **Description**: Current condition of the car model, updated based on events.
- **Editable**: True
- **Show**: True
- **Purpose**: Maintains a dynamic record of the model's physical state.