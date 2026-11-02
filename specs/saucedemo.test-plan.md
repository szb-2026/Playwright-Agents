# Sauce Demo Web Application Test Plan

## Application Overview

Functional test plan for Sauce Demo (Swag Labs), supplied at https://www.saucedemo.com/v1/index.html, which redirects to https://www.saucedemo.com/. Scope includes authentication, product catalog and sorting, item details, cart behavior, checkout validation and completion, and global navigation. Use documented demo credentials where applicable: standard_user / secret_sauce. Each scenario assumes a fresh application state and is independent of other scenarios.

## Test Scenarios

### 1. Authentication

**Seed:** `tests/seed.spec.ts`

#### 1.1. Login with a valid standard account

**File:** `tests/authentication/valid-login.spec.ts`

**Steps:**
  1. Open https://www.saucedemo.com/v1/index.html and confirm the page redirects to the Swag Labs login page.
    - expect: The Swag Labs login form is visible with Username, Password, and Login controls.
  2. Enter standard_user as the username and secret_sauce as the password, then select Login.
    - expect: The user is taken to the Products inventory page.
    - expect: The catalog displays six products and the cart is initially empty.

#### 1.2. Reject missing and invalid credentials

**File:** `tests/authentication/invalid-login.spec.ts`

**Steps:**
  1. Start from the fresh login page and select Login without entering either field.
    - expect: The login is rejected and an inline error identifies the required username.
  2. Dismiss the error if necessary, enter a valid username with an incorrect password, and select Login.
    - expect: The login is rejected and an authentication error is displayed.
    - expect: The user remains on the login page.

#### 1.3. Reject a locked-out account

**File:** `tests/authentication/locked-out-user.spec.ts`

**Steps:**
  1. From the fresh login page, enter locked_out_user and secret_sauce, then select Login.
    - expect: The user is not authenticated.
    - expect: A clear locked-out account error is displayed and the login form remains available.

### 2. Catalog and Products

**Seed:** `tests/seed.spec.ts`

#### 2.1. Sort the catalog by each supported order

**File:** `tests/catalog/product-sorting.spec.ts`

**Steps:**
  1. Log in as standard_user and inspect the initial catalog order.
    - expect: Six products are shown in Name (A to Z) order by default.
  2. Choose Name (Z to A), then Price (low to high), then Price (high to low) from Sort products.
    - expect: Each selection is reflected as the selected sort option.
    - expect: Product order updates accordingly for descending names, ascending prices, and descending prices.
    - expect: The ascending-price list begins with Sauce Labs Onesie ($7.99), and the descending-price list begins with Sauce Labs Fleece Jacket ($49.99).

#### 2.2. View product details and add an item

**File:** `tests/catalog/product-details.spec.ts`

**Steps:**
  1. Log in as standard_user and open the Sauce Labs Onesie product from the catalog.
    - expect: The product detail page shows the matching product name, image, description, and $7.99 price.
    - expect: Back to products and Add to cart controls are available.
  2. Select Add to cart, then select Back to products.
    - expect: The cart badge shows one item.
    - expect: The catalog is shown again and the item is represented as added to the cart.

### 3. Cart and Checkout

**Seed:** `tests/seed.spec.ts`

#### 3.1. Manage cart contents and continue shopping

**File:** `tests/checkout/cart-management.spec.ts`

**Steps:**
  1. Log in as standard_user, add Sauce Labs Backpack and Sauce Labs Bike Light, and open the cart.
    - expect: The cart badge and cart contents show two items with the correct names and prices.
  2. Remove Sauce Labs Backpack from the cart.
    - expect: The backpack is removed and the cart badge and remaining item count update to one.
  3. Select Continue Shopping.
    - expect: The Products inventory page is displayed and the remaining cart item is preserved.

#### 3.2. Validate required checkout information

**File:** `tests/checkout/checkout-validation.spec.ts`

**Steps:**
  1. Log in, add one product, open the cart, select Checkout, and select Continue without filling any checkout fields.
    - expect: Checkout remains on the information step and an error states that First Name is required.
  2. Enter a first name only and select Continue.
    - expect: Checkout remains on the information step and an error states that Last Name is required.
  3. Enter a last name while leaving Zip/Postal Code empty, then select Continue.
    - expect: Checkout remains on the information step and an error states that Zip/Postal Code is required.

#### 3.3. Cancel checkout and return to cart

**File:** `tests/checkout/cancel-checkout.spec.ts`

**Steps:**
  1. Log in, add a product, open the cart, select Checkout, and select Cancel.
    - expect: The user returns to the cart page.
    - expect: The previously added product remains in the cart.

#### 3.4. Complete a purchase and verify the order confirmation

**File:** `tests/checkout/complete-order.spec.ts`

**Steps:**
  1. Log in, add Sauce Labs Backpack ($29.99), open the cart, select Checkout, and enter Ada, Lovelace, and postal code 10001 in the corresponding fields.
    - expect: All three checkout fields contain the entered values.
  2. Select Continue to reach Checkout: Overview.
    - expect: The overview lists the backpack and its $29.99 item total.
    - expect: Payment and shipping information are displayed.
    - expect: The shown tax is $2.40 and the total is $32.39.
  3. Select Finish.
    - expect: The Checkout: Complete! page displays Thank you for your order!
    - expect: The cart is empty.
    - expect: Back Home is available.

### 4. Navigation and Session

**Seed:** `tests/seed.spec.ts`

#### 4.1. Log out from the global menu

**File:** `tests/navigation/logout.spec.ts`

**Steps:**
  1. Log in as standard_user, open the global menu, and select Logout.
    - expect: The user is returned to the Swag Labs login page.
    - expect: The authenticated inventory page is no longer displayed.

#### 4.2. Reset application state from the global menu

**File:** `tests/navigation/reset-state.spec.ts`

**Steps:**
  1. Log in, add a product to the cart, open the global menu, and select Reset App State.
    - expect: The cart badge and cart contents are cleared.
    - expect: The application returns to its baseline state with no products marked as added.
