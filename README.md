# MERN Stack Book Store

This is a full-stack Book Store application built using the MERN stack (MongoDB, Express.js, React.js, and Node.js).

The application allows users to perform CRUD (Create, Read, Update, Delete) operations on a collection of books.

## Features

- **View** a list of all books.
- **Show** details for a single book.
- **Create** a new book entry.
- **Edit** an existing book's information.
- **Delete** a book from the collection.

## Tech Stack

### Backend
- **Node.js**: JavaScript runtime environment.
- **Express.js**: Web framework for Node.js.
- **MongoDB**: NoSQL database for storing book information.
- **Mongoose**: ODM library for MongoDB and Node.js.
- **CORS**: Middleware for enabling Cross-Origin Resource Sharing.

### Frontend
- **React.js**: JavaScript library for building user interfaces.
- **Vite**: Next-generation frontend tooling for fast development.
- **React Router**: For declarative routing in the React application.
- **Axios**: For making HTTP requests to the backend API.
- **Tailwind CSS**: A utility-first CSS framework for styling.
- **Notistack**: For displaying notifications (snackbars).

## Project Structure

```
.
├── backend/
│   ├── models/
│   │   └── bookModel.js      # Mongoose schema for books
│   ├── routes/
│   │   └── booksRoute.js     # API routes for books
│   ├── config.js             # Configuration file (PORT, MongoDB URL)
│   ├── index.js              # Backend entry point
│   └── package.json          # Backend dependencies and scripts
│
└── frontend/
    ├── src/
    │   ├── components/         # Reusable React components
    │   ├── pages/              # React components for different pages
    │   ├── App.jsx             # Main App component with routing
    │   ├── index.css           # Global styles and Tailwind directives
    │   └── main.jsx            # Frontend entry point
    ├── tailwind.config.js    # Tailwind CSS configuration
    ├── vite.config.js        # Vite configuration
    └── package.json          # Frontend dependencies and scripts
```

## Getting Started

### Prerequisites

- Node.js (v14 or later)
- npm
- A free MongoDB Atlas account or a local MongoDB instance.

### Installation & Setup

1.  **Clone the repository:**
    ```bash
    git clone https://github.com/your-username/Book-Store-MERN-Stack.git
    cd Book-Store-MERN-Stack
    ```

2.  **Setup Backend:**
    - Navigate to the backend directory:
      ```bash
      cd backend
      ```
    - Install dependencies:
      ```bash
      npm install
      ```
    - Create a `config.js` file in the `backend` directory or update the existing one with your MongoDB connection string.
      ```javascript
      // backend/config.js
      export const PORT = 5555;
      export const mongoDBURL = 'YOUR_MONGODB_CONNECTION_STRING';
      ```

3.  **Setup Frontend:**
    - Navigate to the frontend directory:
      ```bash
      cd ../frontend
      ```
    - Install dependencies:
      ```bash
      npm install
      ```

### Running the Application

1.  **Start the Backend Server:**
    - From the `backend` directory, run:
      ```bash
      npm run dev
      ```
    - The server will start on `http://localhost:5555`.

2.  **Start the Frontend Development Server:**
    - From the `frontend` directory, run:
      ```bash
      npm run dev
      ```
    - The application will be accessible at `http://localhost:5173` (or another port if 5173 is in use).
