# iPhone Shopping System - Code Explanation

## Overview
This is a command-line based shopping system for an iPhone store that allows customers to browse products, manage a shopping cart, apply discounts, and generate invoices. The system is built using Python with a modular approach using functions.

## Code Structure

### 1. **Product Catalog Management**

#### `display_catalog(catalog)`
```python
def display_catalog(catalog):
    print("\n--- Product Catalog ---")
    if not catalog:
        print("No products available in the catalog.")
        return

    print(f"{'Product':<25} {'Price':>12}")
    print("-" * 40)
    for product, price in catalog.items():
        print(f"{product:<25} GH₵{price:>10.2f}")
    print("-" * 40)
```

**Purpose**: Displays all available products with their prices in a formatted table.

**Key Features**:
- Uses string formatting for aligned columns (`<25` = left-aligned, 25 chars; `:>12` = right-aligned, 12 chars)
- Handles empty catalog gracefully
- Shows prices in Ghana Cedis (GH₵) currency

---

### 2. **Shopping Cart Operations**

#### `add_to_cart(cart, catalog)`
```python
def add_to_cart(cart, catalog):
    while True:
        product_name = input("Enter product name to add (or 'done' to finish): ").strip().title()
        if product_name.lower() == 'done':
            break

        if product_name not in catalog:
            print(f"'{product_name}' is not in the catalog.")
            # Show available products for reference
            continue

        # Quantity input with validation
        while True:
            try:
                quantity = int(input(f"Enter quantity for {product_name}: "))
                if quantity <= 0:
                    print("Quantity must be a positive number.")
                else:
                    break
            except ValueError:
                print("Invalid quantity. Please enter a whole number.")

        # Update cart
        cart[product_name] = cart.get(product_name, 0) + quantity
```

**Purpose**: Allows users to add products to their shopping cart.

**Key Features**:
- **Input Validation**: Ensures valid product names and positive quantities
- **Error Handling**: Uses try-except for non-integer inputs
- **User-Friendly**: Shows available products when invalid name entered
- **Accumulative**: If product already exists, adds to existing quantity
- **Case Handling**: Uses `.title()` for consistent product name formatting

#### `view_cart(cart, catalog)`
```python
def view_cart(cart, catalog):
    print(f"{'Product':<25} {'Qty':>5} {'Unit Price':>12} {'Subtotal':>12}")
    print("-" * 60)
    total_before_discount = 0
    for product, quantity in cart.items():
        if product in catalog:
            unit_price = catalog[product]
            subtotal = unit_price * quantity
            total_before_discount += subtotal
            print(f"{product:<25} {quantity:>5} GH₵{unit_price:>10.2f} GH₵{subtotal:>10.2f}")
```

**Purpose**: Displays current cart contents with calculations.

**Key Features**:
- **Formatted Display**: Aligned columns for easy reading
- **Real-time Calculations**: Shows unit price, quantity, and subtotal
- **Running Total**: Calculates total before discounts
- **Error Prevention**: Checks if products still exist in catalog

#### `update_cart(cart, catalog)`
**Purpose**: Allows modification or removal of cart items.

**Key Features**:
- **Flexible Updates**: Change quantity or remove items (quantity = 0)
- **Confirmation System**: Asks before continuing with multiple updates
- **Input Validation**: Handles invalid quantities and provides cancel option

---

### 3. **Discount System**

#### `calculate_discount_for_product(quantity, buy_n, get_m_free)`
```python
def calculate_discount_for_product(quantity, buy_n, get_m_free):
    if quantity < buy_n:
        return 0

    # How many complete discount sets can we apply?
    discount_sets = quantity // buy_n
    free_items = discount_sets * get_m_free

    # Don't give more free items than total quantity
    return min(free_items, quantity)
```

**Purpose**: Calculates "Buy N, Get M Free" discounts for individual products.

**Logic Example**:
- Buy 3, Get 1 Free + Customer buys 7 items
- `discount_sets = 7 // 3 = 2` (two complete sets)
- `free_items = 2 * 1 = 2` (two free items)
- Customer pays for 5, gets 2 free

#### `calculate_total(cart, catalog, discounts)`
**Purpose**: Calculates final total with all applicable discounts.

**Process**:
1. Iterate through each cart item
2. Check if discount rules apply
3. Calculate free items using discount function
4. Compute final cost (total items - free items) × unit price
5. Track discount details for invoice

---

### 4. **Invoice Generation**

#### `print_invoice(cart, catalog, discounts)`
```python
def print_invoice(cart, catalog, discounts):
    print("\n" + "=" * 70)
    print("                    IPHONE DEALERS SHOP")
    print("                       GROUP 22")
    print("                     FINAL INVOICE")
    print("=" * 70)

    # Calculate totals and discounts
    total_cost, applied_discounts, discount_details = calculate_total(cart, catalog, discounts)
```

**Purpose**: Generates professional-looking invoice with discount breakdown.

**Features**:
- **Professional Layout**: Headers, borders, aligned columns
- **Detailed Breakdown**: Shows unit prices, line totals, discounts
- **Discount Summary**: Lists all applied promotions
- **Financial Summary**: Subtotal, savings, grand total

---

### 5. **System Validation**

#### `validate_system(catalog, discounts)`
**Purpose**: Checks for configuration errors before system starts.

**Validations**:
- **Discount Integrity**: Ensures discount products exist in catalog
- **Data Quality**: Checks for trailing spaces in product names
- **Error Prevention**: Warns about potential matching issues

---

### 6. **Main Application Logic**

#### `main_menu()`
**Purpose**: Central control system that orchestrates the entire application.

**Data Structures**:
```python
# Product catalog - Dictionary mapping product names to prices
catalog = {
    "Iphone 11": 2100.99,
    "Iphone 11 Pro": 2333.99,
    # ... more products
}

# Shopping cart - Dictionary mapping product names to quantities
cart = {}

# Discount rules - Nested dictionary structure
discounts = {
    "Iphone 11": {"type": "buy_n_get_m_free", "buy": 3, "get_free": 1},
    "Iphone 12": {"type": "buy_n_get_m_free", "buy": 2, "get_free": 1},
}
```

**Menu System**:
```
1. View Products          → display_catalog()
2. Add Item to Cart       → add_to_cart()
3. View Cart             → view_cart()
4. Update/Remove Item    → update_cart()
5. Checkout              → print_invoice()
6. Exit                  → Graceful shutdown
```

---

## Key Programming Concepts Used

### 1. **Data Structures**
- **Dictionaries**: For catalog (product→price), cart (product→quantity), discounts
- **Lists**: For temporary storage of discount details and validation errors

### 2. **Error Handling**
```python
try:
    quantity = int(input(f"Enter quantity for {product_name}: "))
    if quantity <= 0:
        print("Quantity must be a positive number.")
except ValueError:
    print("Invalid quantity. Please enter a whole number.")
```

### 3. **String Formatting**
```python
# Alignment and precision control
print(f"{product:<25} GH₵{price:>10.2f}")
# <25: left-aligned, 25 characters
# >10.2f: right-aligned, 10 characters, 2 decimal places
```

### 4. **Input Validation**
- Type checking (int conversion)
- Range validation (positive numbers)
- Existence checking (product in catalog)
- Case-insensitive comparisons

### 5. **Modular Design**
- Each function has a single responsibility
- Functions can be tested independently
- Easy to maintain and extend

---

## System Flow

```
1. System Startup
   ↓
2. Validate Configuration
   ↓
3. Display Main Menu
   ↓
4. User Selection
   ├── View Products → Display Catalog → Return to Menu
   ├── Add to Cart → Show Catalog → Add Items → Return to Menu
   ├── View Cart → Show Cart Contents → Return to Menu
   ├── Update Cart → Modify Items → Return to Menu
   ├── Checkout → Generate Invoice → Continue/Exit Option
   └── Exit → Confirmation if Cart Not Empty → Goodbye
```

---

## Advanced Features

### 1. **Flexible Discount System**
- Supports multiple discount types (currently "buy_n_get_m_free")
- Easy to extend with new discount rules
- Automatic application based on product and quantity

### 2. **Robust User Experience**
- Input validation prevents crashes
- Clear error messages guide users
- Confirmation dialogs prevent accidental actions
- Formatted displays improve readability

### 3. **Business Logic**
- Inventory checking (products must exist in catalog)
- Accumulative cart behavior (multiple additions combine)
- Professional invoice generation
- Financial accuracy with proper decimal handling

---

## Potential Enhancements

1. **Data Persistence**: Save cart/orders to files
2. **User Accounts**: Multiple customer support
3. **Inventory Management**: Stock tracking
4. **Payment Processing**: Multiple payment methods
5. **Reporting**: Sales analytics and reports
6. **GUI Interface**: Replace command-line with graphical interface

This system demonstrates solid programming fundamentals including data structures, error handling, user interface design, and business logic implementation.
