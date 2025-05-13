# Swypt Checkout SDK Documentation

## Introduction

The Swypt Checkout SDK is a comprehensive payment solution that enables seamless crypto and traditional payment processing for your Next.js applications. This documentation covers the integration of the Deposit Modal component.

## Requirements

- Next.js 14.1.4+
- React 18.0.0+
- Node.js 16.0.0+

## Installation

Install the latest version of the Swypt Checkout SDK:

```bash
# Using npm
npm install swypt-checkout@latest

# Using yarn
yarn add swypt-checkout@latest
```

## Setup Instructions

### Set up the Root Layout

Create or modify your root layout file (`app/layout.tsx`) to include the `CryptoProvider`:

```tsx
"use client"

import { CryptoProvider } from "swypt-checkout";
import "./globals.css";

export default function RootLayout({
  children,
}: Readonly<{
  children: React.ReactNode;
}>) {
  return (
    <CryptoProvider>
      <html lang="en">
        <body>{children}</body>
      </html>
    </CryptoProvider>
  );
}
```

> **Important**: The `"use client"` directive is essential as the `CryptoProvider` component contains client-side code.

## Implementing the Deposit Modal

### Basic Implementation

Here's a simple implementation of the Deposit Modal:

```tsx
"use client"

import React, { useState } from "react";
import { DepositModal } from "swypt-checkout";
import "swypt-checkout/dist/styles.css";

export default function PaymentPage() {
  const [isOpen, setIsOpen] = useState(false);
  
  return (
    <>
      <button onClick={() => setIsOpen(true)}>
        Open Deposit Modal
      </button>

      <DepositModal
        isOpen={isOpen}
        onClose={() => setIsOpen(false)}
        headerBackgroundColor="linear-gradient(to right, #DD268A, #FF4040)"
        businessName="Your Business"
        merchantName="Your Merchant"
        merchantAddress="0x6d19a24D93379D1bA58d28884fFBBEf1bc145387"
        amount={100}
      />
    </>
  );
}
```

### Advanced Implementation

For a more comprehensive implementation with styling and user input for deposit amount:

```tsx
"use client"

import React, { useState, ChangeEvent } from "react";
import { DepositModal } from "swypt-checkout";
import "swypt-checkout/dist/styles.css";

export default function PaymentPage() {
  // State to control the modal visibility
  const [isDepositOpen, setIsDepositOpen] = useState(false);
  
  // State to control deposit amount
  const [depositAmount, setDepositAmount] = useState(100);
  
  // Example merchant address - replace with your actual address
  const merchantAddress = "0x6d19a24D93379D1bA58d28884fFBBEf1bc145387";
  
  // Function to handle deposit amount change
  const handleAmountChange = (e: ChangeEvent<HTMLInputElement>) => {
    setDepositAmount(parseFloat(e.target.value));
  };

  // Function to handle modal open
  const handleOpenModal = () => {
    // Prevent body scrolling when modal is open
    document.body.style.overflow = 'hidden';
    setIsDepositOpen(true);
  };

  // Function to handle modal close
  const handleCloseModal = () => {
    // Restore body scrolling when modal is closed
    document.body.style.overflow = 'auto';
    setIsDepositOpen(false);
  };

  return (
    <div className="min-h-screen bg-gray-100 p-4">
      <div className="max-w-md mx-auto bg-white rounded-xl shadow-md overflow-hidden p-6">
        <h1 className="text-2xl font-bold mb-6">Make a Deposit</h1>
        
        <div className="space-y-4">
          <div>
            <label className="block text-sm font-medium text-gray-700 mb-1">
              Amount to Deposit
            </label>
            <div className="relative mt-1 rounded-md shadow-sm">
              <div className="absolute inset-y-0 left-0 pl-3 flex items-center pointer-events-none">
                <span className="text-gray-500 sm:text-sm">$</span>
              </div>
              <input
                type="number"
                value={depositAmount}
                onChange={handleAmountChange}
                className="focus:ring-indigo-500 focus:border-indigo-500 block w-full pl-7 pr-3 py-2 sm:text-sm border border-gray-300 rounded-md"
                placeholder="0.00"
                min="1"
                step="0.01"
              />
            </div>
          </div>
          
          <button
            onClick={handleOpenModal}
            className="w-full flex justify-center py-2 px-4 border border-transparent rounded-md shadow-sm text-sm font-medium text-white bg-indigo-600 hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500"
          >
            Open Deposit Modal
          </button>
        </div>
      </div>
      
      {/* Modal container with overlay */}
      {isDepositOpen && (
        <div className="fixed inset-0 z-50 flex items-center justify-center">
          {/* Backdrop */}
          <div 
            className="fixed inset-0 bg-black/80 backdrop-blur-sm" 
            onClick={handleCloseModal}
          />
          
          {/* Modal content */}
          <div className="relative z-50">
            <DepositModal
              isOpen={isDepositOpen}
              onClose={handleCloseModal}
              headerBackgroundColor="linear-gradient(to right, #044639, #FF4040)"
              businessName="Your Business Name"
              merchantName="Your Merchant Name"
              merchantAddress={merchantAddress}
              amount={depositAmount}
            />
          </div>
        </div>
      )}
    </div>
  );
}
```

## Component API Reference

### DepositModal Props

| Prop                  | Type      | Required | Description                                    |
|-----------------------|-----------|----------|------------------------------------------------|
| isOpen                | boolean   | Yes      | Controls the visibility of the modal           |
| onClose               | () => void| Yes      | Function called when the modal is closed       |
| headerBackgroundColor | string    | No       | Background color or gradient for the modal header |
| businessName          | string    | Yes      | Your business name displayed in the modal      |
| merchantName          | string    | Yes      | The name of the merchant                       |
| merchantAddress       | string    | Yes      | Blockchain address to receive funds            |
| amount                | number    | Yes      | Amount to deposit in fiat currency             |
| cryptoAmount          | number    | No       | Alternative to amount (in crypto)              |

### CryptoProvider

The `CryptoProvider` component does not accept any props but must wrap your application to provide the necessary context for the Swypt components.

## The Deposit Flow

The Deposit Modal implements a multi-step flow:

1. **Asset Selection**: Choose cryptocurrency for the deposit
2. **Choose Method**: Select the payment method
3. **Phone Entry**: Enter your phone number
4. **Confirm Transaction**: Review deposit details
5. **Processing**: Complete the deposit
6. **Success**: View and download the receipt

## Advanced Usage

### Modal Overlay Implementation

For the best user experience, implement the deposit modal as an overlay:

```tsx
{isDepositOpen && (
  <div className="fixed inset-0 z-50 flex items-center justify-center">
    {/* Backdrop */}
    <div 
      className="fixed inset-0 bg-black/80 backdrop-blur-sm" 
      onClick={handleCloseModal}
    />
    
    {/* Modal content */}
    <div className="relative z-50">
      <DepositModal
        isOpen={isDepositOpen}
        onClose={handleCloseModal}
        headerBackgroundColor="linear-gradient(to right, #044639, #FF4040)"
        businessName="Your Business Name"
        merchantName="Your Merchant Name"
        merchantAddress={merchantAddress}
        amount={depositAmount}
      />
    </div>
  </div>
)}
```

### Scroll Management

To prevent the page from scrolling when the modal is open:

```tsx
// Function to handle modal open
const handleOpenModal = () => {
  // Prevent body scrolling when modal is open
  document.body.style.overflow = 'hidden';
  setIsDepositOpen(true);
};

// Function to handle modal close
const handleCloseModal = () => {
  // Restore body scrolling when modal is closed
  document.body.style.overflow = 'auto';
  setIsDepositOpen(false);
};
```

### Custom Themes

To create a custom theme for the modal, use the `headerBackgroundColor` prop:

- Solid Color: `"#044639"`
- Gradient: `"linear-gradient(to right, #044639, #FF4040)"`

## Troubleshooting

### Hydration Errors

If you encounter hydration errors, ensure:
1. Both the parent component and the layout containing the `CryptoProvider` have the `"use client"` directive
2. You're properly handling the modal state

### Modal Opens on a New Page

If the modal opens as a new page instead of an overlay:
1. Verify you've implemented the modal container with the proper CSS positions and z-index
2. Make sure the modal container is rendered within the same component, not through routing

## Best Practices

1. **Merchant Address Security**: Always ensure your merchant address is properly secured and validated
2. **Error Handling**: Implement proper error handling for user inputs
3. **Responsive Design**: Test the modal on different device sizes
4. **Loading States**: Add loading states when necessary
5. **Confirmation**: Provide clear confirmation messages to users after transactions
6. **Privacy**: Ensure user data is handled according to privacy regulations

## Support and Resources

- Email: support@swypt.io
- GitHub: [GitHub Repository](https://github.com/swypt/checkout-sdk)
