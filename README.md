# 🧪 FieldFaker - Mock Data Generator

**FieldFaker** is a Vue 3-based mock data generator that allows you to quickly and easily create fake JSON data. It's especially useful for developers who need dummy data for testing or UI development.

## 🔗 GitHub Repository

➡️ [https://github.com/polashmahmud/FieldFaker](https://github.com/polashmahmud/FieldFaker)

## 🎯 Features

- ✅ Two data generation modes: **Form Mode** and **Interface Mode**
- ✅ Built with Vue 3 Composition API
- ✅ Supports a wide variety of field types using **Faker.js**
- ✅ Custom field name and type inputs
- ✅ Generate multiple records at once
- ✅ Copy the result in JSON format with a single click

## 🖥️ Modes

### 1. Form Mode
Manually select field names and types to generate fake data.

### 2. Interface Mode
Paste a TypeScript interface and generate fake data based on its structure.

## 📦 Supported Field Types

| Key         | Label           |
|-------------|-----------------|
| `name`      | Full Name       |
| `email`     | Email           |
| `phone`     | Phone Number    |
| `address`   | Street Address  |
| `city`      | City            |
| `country`   | Country         |
| `uuid`      | UUID            |
| `number`    | Random Number   |
| `date`      | Past Date       |
| `birthdate` | Birthdate       |
| `imageUrl`  | Image URL       |
| `url`       | URL             |
| `ip`        | IP Address      |
| `password`  | Password        |
| ...         | and many more!  |

## 🚀 Getting Started

### Step 1: Clone the repository

```bash
git clone https://github.com/polashmahmud/FieldFaker.git
cd FieldFaker
````

### Step 2: Install dependencies

```bash
npm install
```

### Step 3: Start the development server

```bash
npm run dev
```

Or with Vite:

```bash
npx vite
```

### Step 4: Open in browser

Visit: [http://localhost:5173](http://localhost:5173)

## 🧑‍💻 Interface Mode Example

```ts
interface User {
  id: string
  name: string
  email: string
  created_at: Date
}
```

Sample output:

```json
{
  "id": "eb5a3a76-313e-4870-9508-f9aa6b168ff8",
  "name": "Rahim Uddin",
  "email": "rahim@example.com",
  "created_at": "2023-08-15T07:21:00.321Z"
}
```

## 📋 License

MIT License © [Polash Mahmud](https://github.com/polashmahmud)
