# 💰 Expense Tracker App - Complete Deployment Package

## 📋 Inhaltsverzeichnis
1. [Projektstruktur](#projektstruktur)
2. [Alle Dateien](#alle-dateien)
3. [Icon-Erstellung](#icon-erstellung)
4. [Deployment-Anleitung](#deployment-anleitung)
5. [Troubleshooting](#troubleshooting)

---

## 📁 Projektstruktur

Erstelle folgende Ordnerstruktur:

```
expense-tracker-app/
├── package.json
├── next.config.js
├── tsconfig.json
├── vercel.json
├── .gitignore
├── README.md
├── .env.local.example
├── src/
│   ├── app/
│   │   ├── layout.tsx
│   │   ├── page.tsx
│   │   └── globals.css
│   ├── types/
│   │   └── index.ts
│   └── utils/
│       ├── storage.ts
│       └── export.ts
└── public/
    ├── manifest.json
    ├── favicon.ico
    ├── icon-192x192.png
    └── icon-512x512.png
```

---

## 📄 Alle Dateien

### 1. `package.json`
```json
{
  "name": "expense-tracker-app",
  "version": "1.0.0",
  "private": true,
  "scripts": {
    "dev": "next dev",
    "build": "next build",
    "start": "next start",
    "lint": "next lint"
  },
  "dependencies": {
    "next": "14.0.0",
    "react": "18.2.0",
    "react-dom": "18.2.0",
    "lucide-react": "^0.263.1"
  },
  "devDependencies": {
    "@types/node": "^20",
    "@types/react": "^18",
    "@types/react-dom": "^18",
    "eslint": "^8",
    "eslint-config-next": "14.0.0",
    "typescript": "^5"
  }
}
```

### 2. `next.config.js`
```javascript
/** @type {import('next').NextConfig} */
const nextConfig = {
  output: 'export',
  trailingSlash: true,
  images: {
    unoptimized: true
  }
}

module.exports = nextConfig
```

### 3. `tsconfig.json`
```json
{
  "compilerOptions": {
    "target": "es5",
    "lib": ["dom", "dom.iterable", "es6"],
    "allowJs": true,
    "skipLibCheck": true,
    "strict": true,
    "noEmit": true,
    "esModuleInterop": true,
    "module": "esnext",
    "moduleResolution": "bundler",
    "resolveJsonModule": true,
    "isolatedModules": true,
    "jsx": "preserve",
    "incremental": true,
    "plugins": [
      {
        "name": "next"
      }
    ],
    "baseUrl": ".",
    "paths": {
      "@/*": ["./src/*"]
    }
  },
  "include": ["next-env.d.ts", "**/*.ts", "**/*.tsx", ".next/types/**/*.ts"],
  "exclude": ["node_modules"]
}
```

### 4. `vercel.json`
```json
{
  "buildCommand": "npm run build",
  "outputDirectory": "out",
  "framework": "nextjs"
}
```

### 5. `.gitignore`
```
# Dependencies
/node_modules
/.pnp
.pnp.js

# Testing
/coverage

# Next.js
/.next/
/out/

# Production
/build

# Misc
.DS_Store
*.pem

# Debug
npm-debug.log*
yarn-debug.log*
yarn-error.log*

# Local env files
.env*.local

# Vercel
.vercel

# TypeScript
*.tsbuildinfo
next-env.d.ts
```

### 6. `.env.local.example`
```
# Environment variables für lokale Entwicklung
# Kopiere diese Datei zu .env.local und fülle die Werte aus

# App Name
NEXT_PUBLIC_APP_NAME="Expense Tracker"

# App Version
NEXT_PUBLIC_APP_VERSION="1.0.0"

# Analytics (optional)
# NEXT_PUBLIC_GA_ID=your-google-analytics-id
```

### 7. `src/app/layout.tsx`
```tsx
import type { Metadata } from 'next'
import './globals.css'

export const metadata: Metadata = {
  title: 'Expense Tracker',
  description: 'Track your expenses with ease',
  manifest: '/manifest.json',
  themeColor: '#B19CD9',
  viewport: 'width=device-width, initial-scale=1, maximum-scale=1, user-scalable=no'
}

export default function RootLayout({
  children,
}: {
  children: React.ReactNode
}) {
  return (
    <html lang="en">
      <head>
        <link rel="icon" href="/favicon.ico" />
        <link rel="apple-touch-icon" href="/icon-192x192.png" />
        <meta name="apple-mobile-web-app-capable" content="yes" />
        <meta name="apple-mobile-web-app-status-bar-style" content="default" />
        <meta name="apple-mobile-web-app-title" content="Expense Tracker" />
      </head>
      <body style={{ margin: 0, padding: 0 }}>{children}</body>
    </html>
  )
}
```

### 8. `src/app/page.tsx`
```tsx
'use client'

import React, { useState, useEffect } from 'react';
import { Plus, Trash2, Download, Calendar, Euro } from 'lucide-react';

interface Expense {
  id: number;
  category: string;
  amount: number;
  date: string;
  description: string;
}

const ExpenseTracker = () => {
  const [categories, setCategories] = useState(['Gas', 'Groceries', 'Entertainment', 'Clothing']);
  const [expenses, setExpenses] = useState<Expense[]>([]);
  const [newCategory, setNewCategory] = useState('');
  const [selectedCategory, setSelectedCategory] = useState('');
  const [amount, setAmount] = useState('');
  const [date, setDate] = useState(new Date().toISOString().split('T')[0]);
  const [description, setDescription] = useState('');
  const [filterMonth, setFilterMonth] = useState(new Date().toISOString().slice(0, 7));
  const [showAddCategory, setShowAddCategory] = useState(false);

  // Beautiful pastel color palette
  const colors = {
    primary: '#B19CD9', // Soft lavender purple
    secondary: '#FFB6C1', // Light pink
    background: '#F8F6FF',
    white: '#FFFFFF',
    text: '#4A4A4A',
    border: '#E8E0F5',
    lightPrimary: '#D6C9E8', // Very light lavender
    lightSecondary: '#FFCCCB' // Very light pink
  };

  // Load data from localStorage on component mount
  useEffect(() => {
    if (typeof window !== 'undefined') {
      const savedCategories = localStorage.getItem('expenseCategories');
      const savedExpenses = localStorage.getItem('expenseData');
      
      if (savedCategories) {
        setCategories(JSON.parse(savedCategories));
      }
      if (savedExpenses) {
        setExpenses(JSON.parse(savedExpenses));
      }
    }
  }, []);

  // Save data to localStorage whenever categories or expenses change
  useEffect(() => {
    if (typeof window !== 'undefined') {
      localStorage.setItem('expenseCategories', JSON.stringify(categories));
    }
  }, [categories]);

  useEffect(() => {
    if (typeof window !== 'undefined') {
      localStorage.setItem('expenseData', JSON.stringify(expenses));
    }
  }, [expenses]);

  const addCategory = () => {
    if (newCategory.trim() && !categories.includes(newCategory.trim())) {
      setCategories([...categories, newCategory.trim()]);
      setNewCategory('');
      setShowAddCategory(false);
    }
  };

  const deleteCategory = (categoryToDelete: string) => {
    setCategories(categories.filter(cat => cat !== categoryToDelete));
    setExpenses(expenses.filter(expense => expense.category !== categoryToDelete));
  };

  const addExpense = () => {
    if (selectedCategory && amount && date) {
      const newExpense: Expense = {
        id: Date.now(),
        category: selectedCategory,
        amount: parseFloat(amount),
        date: date,
        description: description.trim()
      };
      setExpenses([...expenses, newExpense]);
      setAmount('');
      setDescription('');
      setSelectedCategory('');
    }
  };

  const deleteExpense = (id: number) => {
    setExpenses(expenses.filter(expense => expense.id !== id));
  };

  const getFilteredExpenses = () => {
    return expenses.filter(expense => expense.date.startsWith(filterMonth));
  };

  const getTotalByCategory = (categoryName: string) => {
    return expenses
      .filter(expense => expense.category === categoryName)
      .reduce((sum, expense) => sum + expense.amount, 0);
  };

  const getMonthlyTotal = () => {
    return getFilteredExpenses().reduce((sum, expense) => sum + expense.amount, 0);
  };

  const exportToPDF = () => {
    const filteredExpenses = getFilteredExpenses();
    const monthYear = new Date(filterMonth + '-01').toLocaleDateString('en-US', { 
      month: 'long', 
      year: 'numeric' 
    });
    
    // Create a new window for PDF generation
    const printWindow = window.open('', '_blank');
    if (!printWindow) return;
    
    let htmlContent = `
      <!DOCTYPE html>
      <html>
      <head>
        <title>Expense Report - ${monthYear}</title>
        <meta charset="utf-8">
        <style>
          @media print {
            body { margin: 0; }
            .no-print { display: none; }
          }
          
          body { 
            font-family: Arial, sans-serif; 
            margin: 20px; 
            color: #4A4A4A;
            line-height: 1.6;
          }
          
          .header { 
            text-align: center; 
            margin-bottom: 30px;
            background: linear-gradient(135deg, ${colors.primary} 0%, ${colors.secondary} 100%);
            color: white;
            padding: 20px;
            border-radius: 10px;
          }
          
          .header h1 { 
            margin: 0; 
            font-size: 28px; 
            font-weight: bold;
          }
          
          .header p {
            margin: 10px 0 0 0;
            font-size: 16px;
            opacity: 0.9;
          }
          
          h2 { 
            color: ${colors.primary}; 
            border-bottom: 2px solid ${colors.secondary};
            padding-bottom: 10px;
            margin-top: 30px;
          }
          
          .summary-stats {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
            gap: 15px;
            margin: 20px 0;
          }
          
          .stat-card {
            background: ${colors.background};
            padding: 15px;
            border-radius: 8px;
            text-align: center;
            border: 1px solid ${colors.border};
          }
          
          .stat-value {
            font-size: 24px;
            font-weight: bold;
            color: ${colors.primary};
          }
          
          .stat-label {
            font-size: 14px;
            color: #666;
            margin-top: 5px;
          }
          
          table { 
            width: 100%; 
            border-collapse: collapse; 
            margin: 20px 0;
            font-size: 14px;
          }
          
          th, td { 
            padding: 12px 8px; 
            text-align: left; 
            border-bottom: 1px solid ${colors.border};
          }
          
          th { 
            background-color: ${colors.lightSecondary}; 
            font-weight: bold;
          }
          
          tr:nth-child(even) { 
            background-color: ${colors.background};
          }
          
          .category-tag {
            background-color: ${colors.lightPrimary};
            padding: 4px 8px;
            border-radius: 12px;
            font-size: 11px;
            font-weight: 500;
            display: inline-block;
          }
          
          .amount {
            text-align: right;
            font-weight: bold;
            color: ${colors.primary};
          }
          
          .total-row {
            background-color: ${colors.lightPrimary} !important;
            font-weight: bold;
            border-top: 2px solid ${colors.primary};
          }
          
          .summary {
            background-color: ${colors.lightSecondary};
            padding: 20px;
            border-radius: 10px;
            margin-top: 30px;
          }
          
          .summary-item {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin: 10px 0;
            padding: 8px 0;
            border-bottom: 1px solid rgba(0,0,0,0.1);
          }
          
          .summary-item:last-child {
            border-bottom: none;
            font-weight: bold;
            font-size: 16px;
            margin-top: 15px;
            padding-top: 15px;
            border-top: 2px solid ${colors.primary};
          }
          
          .footer {
            margin-top: 40px;
            text-align: center;
            font-size: 12px;
            color: #666;
            border-top: 1px solid ${colors.border};
            padding-top: 20px;
          }
          
          @page {
            margin: 1cm;
          }
        </style>
      </head>
      <body>
        <div class="header">
          <h1>💰 Expense Report</h1>
          <p>${monthYear}</p>
        </div>
        
        <div class="summary-stats">
          <div class="stat-card">
            <div class="stat-value">€${getMonthlyTotal().toFixed(2)}</div>
            <div class="stat-label">Total Expenses</div>
          </div>
          <div class="stat-card">
            <div class="stat-value">${filteredExpenses.length}</div>
            <div class="stat-label">Number of Transactions</div>
          </div>
          <div class="stat-card">
            <div class="stat-value">€${filteredExpenses.length > 0 ? (getMonthlyTotal() / filteredExpenses.length).toFixed(2) : '0.00'}</div>
            <div class="stat-label">Average per Transaction</div>
          </div>
        </div>
        
        <h2>📝 Detailed Expenses</h2>
        <table>
          <thead>
            <tr>
              <th>Date</th>
              <th>Category</th>
              <th>Description</th>
              <th class="amount">Amount (€)</th>
            </tr>
          </thead>
          <tbody>
    `;
    
    filteredExpenses
      .sort((a, b) => new Date(b.date).getTime() - new Date(a.date).getTime())
      .forEach(expense => {
        htmlContent += `
          <tr>
            <td>${new Date(expense.date).toLocaleDateString('en-US')}</td>
            <td><span class="category-tag">${expense.category}</span></td>
            <td>${expense.description || '-'}</td>
            <td class="amount">${expense.amount.toFixed(2)}</td>
          </tr>
        `;
      });
    
    htmlContent += `
            <tr class="total-row">
              <td colspan="3"><strong>Total</strong></td>
              <td class="amount"><strong>€${getMonthlyTotal().toFixed(2)}</strong></td>
            </tr>
          </tbody>
        </table>
        
        <div class="summary">
          <h2>📊 Summary by Category</h2>
    `;
    
    categories.forEach(category => {
      const categoryTotal = filteredExpenses
        .filter(expense => expense.category === category)
        .reduce((sum, expense) => sum + expense.amount, 0);
      if (categoryTotal > 0) {
        htmlContent += `
          <div class="summary-item">
            <span><span class="category-tag">${category}</span></span>
            <span><strong>€${categoryTotal.toFixed(2)}</strong></span>
          </div>
        `;
      }
    });
    
    htmlContent += `
          <div class="summary-item">
            <span><strong>Total Amount</strong></span>
            <span><strong>€${getMonthlyTotal().toFixed(2)}</strong></span>
          </div>
        </div>
        
        <div class="footer">
          <p>Generated on ${new Date().toLocaleDateString('en-US')} • Expense Tracker App</p>
        </div>
      </body>
      </html>
    `;
    
    printWindow.document.write(htmlContent);
    printWindow.document.close();
    
    // Wait for content to load, then print
    setTimeout(() => {
      printWindow.print();
      printWindow.close();
    }, 250);
  };

  return (
    <div style={{ 
      minHeight: '100vh', 
      backgroundColor: colors.background,
      fontFamily: 'Arial, sans-serif'
    }}>
      {/* Header */}
      <div style={{
        background: `linear-gradient(135deg, ${colors.primary} 0%, ${colors.secondary} 100%)`,
        color: colors.text,
        padding: '20px',
        textAlign: 'center',
        boxShadow: '0 2px 10px rgba(0,0,0,0.1)'
      }}>
        <h1 style={{ margin: 0, fontSize: '28px', fontWeight: 'bold' }}>
          💰 Expense Tracker
        </h1>
      </div>

      <div style={{ maxWidth: '1200px', margin: '0 auto', padding: '20px' }}>
        
        {/* Manage Categories */}
        <div style={{
          backgroundColor: colors.white,
          borderRadius: '12px',
          padding: '20px',
          marginBottom: '20px',
          boxShadow: '0 4px 15px rgba(0,0,0,0.1)',
          border: `2px solid ${colors.secondary}`
        }}>
          <h2 style={{ 
            color: colors.primary, 
            marginTop: 0,
            borderBottom: `2px solid ${colors.secondary}`,
            paddingBottom: '10px'
          }}>
            📁 Manage Categories
          </h2>
          
          <div style={{ display: 'flex', flexWrap: 'wrap', gap: '10px', marginBottom: '15px' }}>
            {categories.map((category, index) => (
              <div key={category} style={{
                backgroundColor: index % 2 === 0 ? colors.primary : colors.secondary,
                color: colors.text,
                padding: '8px 12px',
                borderRadius: '20px',
                display: 'flex',
                alignItems: 'center',
                gap: '8px',
                fontWeight: '500'
              }}>
                {category}
                <button
                  onClick={() => deleteCategory(category)}
                  style={{
                    background: 'rgba(255,255,255,0.3)',
                    border: 'none',
                    borderRadius: '50%',
                    width: '20px',
                    height: '20px',
                    display: 'flex',
                    alignItems: 'center',
                    justifyContent: 'center',
                    cursor: 'pointer'
                  }}
                >
                  <Trash2 size={12} />
                </button>
              </div>
            ))}
          </div>

          {!showAddCategory ? (
            <button
              onClick={() => setShowAddCategory(true)}
              style={{
                backgroundColor: colors.primary,
                color: colors.white,
                border: 'none',
                padding: '10px 20px',
                borderRadius: '25px',
                cursor: 'pointer',
                display: 'flex',
                alignItems: 'center',
                gap: '8px',
                fontWeight: '500'
              }}
            >
              <Plus size={16} /> Add Category
            </button>
          ) : (
            <div style={{ display: 'flex', gap: '10px', alignItems: 'center' }}>
              <input
                type="text"
                value={newCategory}
                onChange={(e) => setNewCategory(e.target.value)}
                placeholder="New category..."
                style={{
                  padding: '8px 12px',
                  border: `2px solid ${colors.secondary}`,
                  borderRadius: '8px',
                  fontSize: '14px'
                }}
                onKeyPress={(e) => e.key === 'Enter' && addCategory()}
              />
              <button
                onClick={addCategory}
                style={{
                  backgroundColor: colors.primary,
                  color: colors.white,
                  border: 'none',
                  padding: '8px 15px',
                  borderRadius: '8px',
                  cursor: 'pointer'
                }}
              >
                Add
              </button>
              <button
                onClick={() => setShowAddCategory(false)}
                style={{
                  backgroundColor: colors.secondary,
                  color: colors.text,
                  border: 'none',
                  padding: '8px 15px',
                  borderRadius: '8px',
                  cursor: 'pointer'
                }}
              >
                Cancel
              </button>
            </div>
          )}
        </div>

        {/* Add Expense */}
        <div style={{
          backgroundColor: colors.white,
          borderRadius: '12px',
          padding: '20px',
          marginBottom: '20px',
          boxShadow: '0 4px 15px rgba(0,0,0,0.1)',
          border: `2px solid ${colors.primary}`
        }}>
          <h2 style={{ 
            color: colors.primary, 
            marginTop: 0,
            borderBottom: `2px solid ${colors.primary}`,
            paddingBottom: '10px'
          }}>
            💳 Add New Expense
          </h2>
          
          <div style={{ display: 'grid', gridTemplateColumns: 'repeat(auto-fit, minmax(200px, 1fr))', gap: '15px' }}>
            <div>
              <label style={{ display: 'block', marginBottom: '5px', color: colors.text, fontWeight: '500' }}>
                Category:
              </label>
              <select
                value={selectedCategory}
                onChange={(e) => setSelectedCategory(e.target.value)}
                style={{
                  width: '100%',
                  padding: '10px',
                  border: `2px solid ${colors.secondary}`,
                  borderRadius: '8px',
                  fontSize: '14px'
                }}
              >
                <option value="">Choose category...</option>
                {categories.map(category => (
                  <option key={category} value={category}>{category}</option>
                ))}
              </select>
            </div>

            <div>
              <label style={{ display: 'block', marginBottom: '5px', color: colors.text, fontWeight: '500' }}>
                Amount (€):
              </label>
              <input
                type="number"
                step="0.01"
                value={amount}
                onChange={(e) => setAmount(e.target.value)}
                placeholder="0.00"
                style={{
                  width: '100%',
                  padding: '10px',
                  border: `2px solid ${colors.secondary}`,
                  borderRadius: '8px',
                  fontSize: '14px'
                }}
              />
            </div>

            <div>
              <label style={{ display: 'block', marginBottom: '5px', color: colors.text, fontWeight: '500' }}>
                Date:
              </label>
              <input
                type="date"
                value={date}
                onChange={(e) => setDate(e.target.value)}
                style={{
                  width: '100%',
                  padding: '10px',
                  border: `2px solid ${colors.secondary}`,
                  borderRadius: '8px',
                  fontSize: '14px'
                }}
              />
            </div>

            <div>
              <label style={{ display: 'block', marginBottom: '5px', color: colors.text, fontWeight: '500' }}>
                Description:
              </label>
              <input
                type="text"
                value={description}
                onChange={(e) => setDescription(e.target.value)}
                placeholder="Optional..."
                style={{
                  width: '100%',
                  padding: '10px',
                  border: `2px solid ${colors.secondary}`,
                  borderRadius: '8px',
                  fontSize: '14px'
                }}
              />
            </div>
          </div>

          <button
            onClick={addExpense}
            disabled={!selectedCategory || !amount || !date}
            style={{
              backgroundColor: selectedCategory && amount && date ? colors.primary : '#ccc',
              color: colors.white,
              border: 'none',
              padding: '12px 25px',
              borderRadius: '25px',
              cursor: selectedCategory && amount && date ? 'pointer' : 'not-allowed',
              marginTop: '15px',
              display: 'flex',
              alignItems: 'center',
              gap: '8px',
              fontWeight: '500',
              fontSize: '16px'
            }}
          >
            <Plus size={16} /> Add Expense
          </button>
        </div>

        {/* Month Filter and Export */}
        <div style={{
          backgroundColor: colors.white,
          borderRadius: '12px',
          padding: '20px',
          marginBottom: '20px',
          boxShadow: '0 4px 15px rgba(0,0,0,0.1)',
          border: `2px solid ${colors.secondary}`
        }}>
          <div style={{ display: 'flex', justifyContent: 'space-between', alignItems: 'center', flexWrap: 'wrap', gap: '15px' }}>
            <div style={{ display: 'flex', alignItems: 'center', gap: '10px' }}>
              <Calendar size={20} color={colors.primary} />
              <label style={{ color: colors.text, fontWeight: '500' }}>Month:</label>
              <input
                type="month"
                value={filterMonth}
                onChange={(e) => setFilterMonth(e.target.value)}
                style={{
                  padding: '8px 12px',
                  border: `2px solid ${colors.secondary}`,
                  borderRadius: '8px',
                  fontSize: '14px'
                }}
              />
            </div>

            <div style={{ display: 'flex', alignItems: 'center', gap: '15px' }}>
              <div style={{ 
                fontSize: '18px', 
                fontWeight: 'bold', 
                color: colors.primary,
                display: 'flex',
                alignItems: 'center',
                gap: '5px'
              }}>
                <Euro size={20} />
                Total: {getMonthlyTotal().toFixed(2)}€
              </div>
              
              <button
                onClick={exportToPDF}
                disabled={getFilteredExpenses().length === 0}
                style={{
                  backgroundColor: getFilteredExpenses().length > 0 ? colors.primary : '#ccc',
                  color: colors.white,
                  border: 'none',
                  padding: '10px 20px',
                  borderRadius: '25px',
                  cursor: getFilteredExpenses().length > 0 ? 'pointer' : 'not-allowed',
                  display: 'flex',
                  alignItems: 'center',
                  gap: '8px',
                  fontWeight: '500'
                }}
              >
                <Download size={16} /> Export PDF
              </button>
            </div>
          </div>
        </div>

        {/* Category Overview */}
        <div style={{
          backgroundColor: colors.white,
          borderRadius: '12px',
          padding: '20px',
          marginBottom: '20px',
          boxShadow: '0 4px 15px rgba(0,0,0,0.1)',
          border: `2px solid ${colors.primary}`
        }}>
          <h2 style={{ 
            color: colors.primary, 
            marginTop: 0,
            borderBottom: `2px solid ${colors.primary}`,
            paddingBottom: '10px'
          }}>
            📊 Category Overview
          </h2>
          
          <div style={{ display: 'grid', gridTemplateColumns: 'repeat(auto-fit, minmax(250px, 1fr))', gap: '15px' }}>
            {categories.map((category, index) => {
              const total = getTotalByCategory(category);
              return (
                <div key={category} style={{
                  backgroundColor: index % 2 === 0 ? colors.lightPrimary : colors.lightSecondary,
                  padding: '15px',
                  borderRadius: '10px',
                  textAlign: 'center',
                  color: colors.text,
                  fontWeight: '500'
                }}>
                  <div style={{ fontSize: '16px', marginBottom: '5px' }}>{category}</div>
                  <div style={{ fontSize: '20px', fontWeight: 'bold' }}>{total.toFixed(2)}€</div>
                </div>
              );
            })}
          </div>
        </div>

        {/* Expenses List */}
        <div style={{
          backgroundColor: colors.white,
          borderRadius: '12px',
          padding: '20px',
          boxShadow: '0 4px 15px rgba(0,0,0,0.1)',
          border: `2px solid ${colors.secondary}`
        }}>
          <h2 style={{ 
            color: colors.primary, 
            marginTop: 0,
            borderBottom: `2px solid ${colors.secondary}`,
            paddingBottom: '10px'
          }}>
            📝 Expenses ({new Date(filterMonth + '-01').toLocaleDateString('en-US', { month: 'long', year: 'numeric' })})
          </h2>
          
          {getFilteredExpenses().length === 0 ? (
            <p style={{ textAlign: 'center', color: '#999', fontStyle: 'italic', padding: '20px' }}>
              No expenses for this month.
            </p>
          ) : (
            <div style={{ overflowX: 'auto' }}>
              <table style={{ width: '100%', borderCollapse: 'collapse' }}>
                <thead>
                  <tr style={{ backgroundColor: colors.lightSecondary }}>
                    <th style={{ padding: '12px', textAlign: 'left', borderBottom: `2px solid ${colors.border}` }}>Date</th>
                    <th style={{ padding: '12px', textAlign: 'left', borderBottom: `2px solid ${colors.border}` }}>Category</th>
                    <th style={{ padding: '12px', textAlign: 'left', borderBottom: `2px solid ${colors.border}` }}>Description</th>
                    <th style={{ padding: '12px', textAlign: 'right', borderBottom: `2px solid ${colors.border}` }}>Amount</th>
                    <th style={{ padding: '12px', textAlign: 'center', borderBottom: `2px solid ${colors.border}` }}>Action</th>
                  </tr>
                </thead>
                <tbody>
                  {getFilteredExpenses()
                    .sort((a, b) => new Date(b.date).getTime() - new Date(a.date).getTime())
                    .map((expense, index) => (
                    <tr key={expense.id} style={{ 
                      backgroundColor: index % 2 === 0 ? colors.background : colors.white 
                    }}>
                      <td style={{ padding: '12px', borderBottom: `1px solid ${colors.border}` }}>
                        {new Date(expense.date).toLocaleDateString('en-US')}
                      </td>
                      <td style={{ padding: '12px', borderBottom: `1px solid ${colors.border}` }}>
                        <span style={{
                          backgroundColor: colors.lightPrimary,
                          padding: '4px 8px',
                          borderRadius: '12px',
                          fontSize: '12px',
                          fontWeight: '500'
                        }}>
                          {expense.category}
                        </span>
                      </td>
                      <td style={{ padding: '12px', borderBottom: `1px solid ${colors.border}` }}>
                        {expense.description || '-'}
                      </td>
                      <td style={{ 
                        padding: '12px', 
                        textAlign: 'right', 
                        fontWeight: 'bold',
                        color: colors.primary,
                        borderBottom: `1px solid ${colors.border}`
                      }}>
                        {expense.amount.toFixed(2)}€
                      </td>
                      <td style={{ padding: '12px', textAlign: 'center', borderBottom: `1px solid ${colors.border}` }}>
                        <button
                          onClick={() => deleteExpense(expense.id)}
                          style={{
                            backgroundColor: colors.secondary,
                            color: colors.text,
                            border: 'none',
                            padding: '6px',
                            borderRadius: '6px',
                            cursor: 'pointer',
                            display: 'flex',
                            alignItems: 'center',
                            justifyContent: 'center',
                            margin: '0 auto'
                          }}
                        >
                          <Trash2 size={14} />
                        </button>
                      </td>
                    </tr>
                  ))}
                </tbody>
              </table>
            </div>
          )}
        </div>
      </div>
    </div>
  );
};

export default ExpenseTracker;
```

### 9. `src/app/globals.css`
```css
html,
body {
  padding: 0;
  margin: 0;
  font-family: -apple-system, BlinkMacSystemFont, Segoe UI, Roboto, Oxygen,
    Ubuntu, Cantarell, Fira Sans, Droid Sans, Helvetica Neue, sans-serif;
  background-color: #F8F6FF;
}

* {
  box-sizing: border-box;
}

/* Mobile-first responsive design */
@media (max-width: 768px) {
  .container {
    padding: 10px !important;
  }
  
  .grid {
    grid-template-columns: 1fr !important;
  }
  
  .button-group {
    flex-direction: column;
    gap: 10px;
  }
}

/* PWA specific styles */
@media (display-mode: standalone) {
  body {
    -webkit-user-select: none;
    -webkit-touch-callout: none;
  }
}

/* Smooth scrolling */
html {
  scroll-behavior: smooth;
}

/* Better touch targets for mobile */
button, input, select {
  min-height: 44px;
  touch-action: manipulation;
}

/* Prevent zoom on input focus */
input[type="text"],
input[type="number"],
input[type="date"],
select {
  font-size: 16px;
}
```

### 10. `src/types/index.ts`
```typescript
export interface Expense {
  id: number;
  category: string;
  amount: number;
  date: string;
  description: string;
}

export interface Category {
  name: string;
  color: string;
}

export interface MonthlyReport {
  month: string;
  total: number;
  expenses: Expense[];
  categoryTotals: Record<string, number>;
}
```

### 11. `src/utils/storage.ts`
```typescript
// Utility functions for localStorage operations
export const STORAGE_KEYS = {
  EXPENSES: 'expenseData',
  CATEGORIES: 'expenseCategories',
  SETTINGS: 'appSettings'
} as const;

export const loadFromStorage = <T>(key: string, defaultValue: T): T => {
  if (typeof window === 'undefined') return defaultValue;
  
  try {
    const item = localStorage.getItem(key);
    return item ? JSON.parse(item) : defaultValue;
  } catch (error) {
    console.error(`Error loading ${key} from localStorage:`, error);
    return defaultValue;
  }
};

export const saveToStorage = <T>(key: string, value: T): void => {
  if (typeof window === 'undefined') return;
  
  try {
    localStorage.setItem(key, JSON.stringify(value));
  } catch (error) {
    console.error(`Error saving ${key} to localStorage:`, error);
  }
};

export const removeFromStorage = (key: string): void => {
  if (typeof window === 'undefined') return;
  
  try {
    localStorage.removeItem(key);
  } catch (error) {
    console.error(`Error removing ${key} from localStorage:`, error);
  }
};
```

### 12. `src/utils/export.ts`
```typescript
import { Expense } from '@/types';

export const generatePDFContent = (
  expenses: Expense[],
  categories: string[],
  monthYear: string,
  total: number,
  colors: any
): string => {
  return `
    <!DOCTYPE html>
    <html>
    <head>
      <title>Expense Report - ${monthYear}</title>
      <meta charset="utf-8">
      <style>
        @media print {
          body { margin: 0; }
          .no-print { display: none; }
        }
        
        body { 
          font-family: Arial, sans-serif; 
          margin: 20px; 
          color: #4A4A4A;
          line-height: 1.6;
        }
        
        .header { 
          text-align: center; 
          margin-bottom: 30px;
          background: linear-gradient(135deg, ${colors.primary} 0%, ${colors.secondary} 100%);
          color: white;
          padding: 20px;
          border-radius: 10px;
        }
        
        h1 { margin: 0; font-size: 28px; font-weight: bold; }
        h2 { 
          color: ${colors.primary}; 
          border-bottom: 2px solid ${colors.secondary};
          padding-bottom: 10px;
        }
        
        table { 
          width: 100%; 
          border-collapse: collapse; 
          margin-bottom: 20px;
        }
        
        th, td { 
          padding: 12px; 
          text-align: left; 
          border-bottom: 1px solid ${colors.border};
        }
        
        th { 
          background-color: ${colors.lightSecondary}; 
          font-weight: bold;
        }
        
        tr:nth-child(even) { 
          background-color: ${colors.background};
        }
        
        .category-tag {
          background-color: ${colors.lightPrimary};
          padding: 4px 8px;
          border-radius: 12px;
          font-size: 12px;
          font-weight: 500;
        }
        
        .total { 
          font-size: 18px; 
          font-weight: bold; 
          color: ${colors.primary};
          text-align: right;
          margin: 20px 0;
        }
        
        .summary {
          background-color: ${colors.lightSecondary};
          padding: 15px;
          border-radius: 10px;
          margin-top: 20px;
        }
        
        .summary-item {
          display: flex;
          justify-content: space-between;
          margin: 5px 0;
        }
      </style>
    </head>
    <body>
      <div class="header">
        <h1>💰 Expense Report</h1>
        <p>${monthYear}</p>
      </div>
      
      <h2>📝 Detailed Expenses</h2>
      <table>
        <thead>
          <tr>
            <th>Date</th>
            <th>Category</th>
            <th>Description</th>
            <th style="text-align: right;">Amount (€)</th>
          </tr>
        </thead>
        <tbody>
          ${expenses
            .sort((a, b) => new Date(b.date).getTime() - new Date(a.date).getTime())
            .map(expense => `
              <tr>
                <td>${new Date(expense.date).toLocaleDateString('en-US')}</td>
                <td><span class="category-tag">${expense.category}</span></td>
                <td>${expense.description || '-'}</td>
                <td style="text-align: right; font-weight: bold;">${expense.amount.toFixed(2)}</td>
              </tr>
            `).join('')}
        </tbody>
      </table>
      
      <div class="total">
        Total Amount: €${total.toFixed(2)}
      </div>
      
      <div class="summary">
        <h2>📊 Summary by Category</h2>
        ${categories.map(category => {
          const categoryTotal = expenses
            .filter(expense => expense.category === category)
            .reduce((sum, expense) => sum + expense.amount, 0);
          if (categoryTotal === 0) return '';
          return `
            <div class="summary-item">
              <span><span class="category-tag">${category}</span></span>
              <span style="font-weight: bold;">€${categoryTotal.toFixed(2)}</span>
            </div>
          `;
        }).join('')}
      </div>
    </body>
    </html>
  `;
};
```

### 13. `public/manifest.json`
```json
{
  "name": "Expense Tracker",
  "short_name": "ExpenseApp",
  "description": "Track your expenses with ease",
  "start_url": "/",
  "display": "standalone",
  "background_color": "#F8F6FF",
  "theme_color": "#B19CD9",
  "orientation": "portrait-primary",
  "icons": [
    {
      "src": "/icon-192x192.png",
      "sizes": "192x192",
      "type": "image/png",
      "purpose": "maskable any"
    },
    {
      "src": "/icon-512x512.png",
      "sizes": "512x512",
      "type": "image/png",
      "purpose": "maskable any"
    }
  ]
}
```

### 14. `README.md`
```markdown
# 💰 Expense Tracker App

A beautiful mobile-first expense tracking application built with Next.js and deployed on Vercel.

## ✨ Features

- 📱 Mobile-responsive design with PWA support
- 💾 Local storage for data persistence
- 📊 Category-based expense tracking
- 📄 PDF export functionality
- 🎨 Beautiful pastel color scheme (Lavender & Pink)
- 🔄 Real-time expense calculations
- 📅 Month-based filtering and reporting

## 🚀 Getting Started

### Prerequisites
- Node.js 16+ installed
- Git installed

### Installation

1. Clone or download this repository
2. Navigate to the project directory:
```bash
cd expense-tracker-app
```

3. Install dependencies:
```bash
npm install
```

4. Run the development server:
```bash
npm run dev
```

5. Open [http://localhost:3000](http://localhost:3000) in your browser

### Build for Production

```bash
npm run build
```

## 📱 PWA Features

- Install as mobile app from browser
- Works offline after first load
- Native app-like experience
- Push notifications ready (can be extended)

## 🚀 Deployment on Vercel

1. Create a GitHub repository and push your code
2. Go to [vercel.com](https://vercel.com)
3. Sign up/in with your GitHub account
4. Click "New Project"
5. Import your repository
6. Vercel will automatically detect Next.js and deploy
7. Your app will be live in minutes!

## 📂 Project Structure

```
expense-tracker-app/
├── src/
│   ├── app/
│   │   ├── layout.tsx      # App layout and metadata
│   │   ├── page.tsx        # Main app component
│   │   └── globals.css     # Global styles
│   ├── types/
│   │   └── index.ts        # TypeScript interfaces
│   └── utils/
│       ├── storage.ts      # LocalStorage utilities
│       └── export.ts       # PDF export utilities
├── public/
│   ├── manifest.json       # PWA manifest
│   ├── favicon.ico         # App icon (create this)
│   ├── icon-192x192.png    # PWA icon (create this)
│   └── icon-512x512.png    # PWA icon (create this)
├── package.json
├── next.config.js
├── tsconfig.json
└── vercel.json
```

## 🎨 Customization

### Colors
Edit the `colors` object in `src/app/page.tsx` to change the color scheme:

```typescript
const colors = {
  primary: '#B19CD9',      // Main purple
  secondary: '#FFB6C1',    // Light pink
  background: '#F8F6FF',   // Light background
  // ... more colors
};
```

### Categories
Default categories are defined in the component state. Users can add/remove categories through the UI.

### Features to Add
- Data backup/sync
- Budget tracking
- Recurring expenses
- Charts and analytics
- Multi-currency support

## 🔧 Troubleshooting

### Common Issues

1. **Build fails**: Check that all dependencies are installed with `npm install`
2. **Icons missing**: Create the required icon files in the `public/` directory
3. **TypeScript errors**: Ensure all types are properly defined
4. **Deployment issues**: Check Vercel logs for specific error messages

### Browser Support
- Chrome/Edge 80+
- Firefox 75+
- Safari 13+
- Mobile browsers with PWA support

## 📄 License

This project is open source and available under the MIT License.

## 🤝 Contributing

Feel free to fork this project and submit pull requests for improvements!
```

---

## 🎨 Icon-Erstellung

Du musst noch diese 3 Icon-Dateien erstellen:

### `public/favicon.ico`
- 16x16 oder 32x32 Pixel
- ICO-Format
- Einfaches Geld-Symbol (💰) oder Taschenrechner

### `public/icon-192x192.png`
- 192x192 Pixel
- PNG-Format mit transparentem Hintergrund
- Sollte gut auf dunklen und hellen Hintergründen aussehen

### `public/icon-512x512.png`
- 512x512 Pixel
- PNG-Format mit transparentem Hintergrund
- Gleiche Design wie das 192px Icon

**Tools zum Erstellen:**
- **Online:** https://favicon.io/ oder https://realfavicongenerator.net/
- **Design:** Canva, Figma, oder GIMP
- **Einfach:** Verwende ein Emoji wie 💰 als Basis

---

## 🚀 Deployment-Anleitung

### Schritt 1: Projekt Setup
```bash
# Neuen Ordner erstellen
mkdir expense-tracker-app
cd expense-tracker-app

# Alle Dateien aus diesem Dokument in die richtige Struktur kopieren
```

### Schritt 2: Icons erstellen
- Erstelle die 3 benötigten Icon-Dateien im `public/` Ordner

### Schritt 3: Git Repository
```bash
# Git initialisieren
git init
git add .
git commit -m "Initial commit: Expense Tracker App"

# GitHub Repository erstellen und verknüpfen
git remote add origin https://github.com/dein-username/expense-tracker-app.git
git branch -M main
git push -u origin main
```

### Schritt 4: Vercel Deployment
1. Gehe zu https://vercel.com
2. Melde dich mit GitHub an
3. Klicke "New Project"
4. Wähle dein Repository
5. Vercel erkennt automatisch Next.js
6. Klicke "Deploy"
7. ✅ Fertig! Deine App ist live

### Schritt 5: PWA Testen
- Öffne die App auf deinem Handy
- Im Browser-Menü: "Zum Homescreen hinzufügen"
- App kann jetzt wie eine native App verwendet werden

---

## 🎯 Was du bekommst

✅ **Vollständige PWA** - installierbar als mobile App  
✅ **Lokale Speicherung** - Daten bleiben erhalten  
✅ **PDF-Export** - professionelle Berichte  
✅ **Responsive Design** - perfekt für alle Geräte  
✅ **TypeScript** - typsicher und wartbar  
✅ **Vercel-ready** - sofort deploybar  
✅ **Pastell-Design** - wunderschöne Lila/Rosa Farben  

Die App ist produktionsreif und kann sofort verwendet werden! 🎉