
- [Week 6](https://learn.foundersandcoders.com/course/syllabus/developer/project-2-week-3/learning-outcomes/)

## Assessment
 ### 1 Backend Development with Express and SQLite
 #### 1.1 Developing a Server using Express
 I set up the server using Express, including middleware for CORS, body parsing, and cookie parsing.

```typescript
import express, { Express } from 'express';
import dotenv from 'dotenv';
import Products from './routes/product.routes';
import cors from 'cors';
import cookieParser from 'cookie-parser';
import bodyParser from 'body-parser';

const app: Express = express();
dotenv.config();
const port = process.env.PORT || 3000;

app.use(cors());
app.use(express.json());
app.use(bodyParser.json());
app.use(cookieParser(process.env.COOKIE_SECRET));

app.listen(port, () => {
    console.log(`Server is running at http://localhost:${port}`);
});
```

#### 1.2 Designing and Implementing a Database Schema
I designed the schema for the database to store products, users, reviews, carts, and sessions, ensuring relational integrity with foreign keys.
```SQL
CREATE TABLE IF NOT EXISTS products (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    name TEXT,
    description TEXT,
    image_path TEXT,
    stock INTEGER,
    category_id INTEGER,
    price REAL,
    created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (category_id) REFERENCES categories(id)
);

CREATE TABLE IF NOT EXISTS users (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    name TEXT,
    email TEXT NOT NULL UNIQUE,
    password TEXT,
    created_at DATETIME DEFAULT CURRENT_TIMESTAMP
);
```

#### 1.4 Implementing CRUD Operations
I implemented CRUD operations for products and carts, enabling interaction with the database.
```typescript
export async function sqlDeleteProduct(productID: number) {
    const deleteProduct = await db.prepare(`
        DELETE FROM products WHERE id = ?
    `).run(productID);
    return deleteProduct;
}

app.delete('/product/:id', async (req: Request, res: Response) => {
    const productId: number = parseInt(req.params.id);
    const deleteProduct = await sqlDeleteProduct(productId);
    res.send(`Product with ID ${productId} deleted successfully`);
});
```

#### 1.5 Secure User Authentication
I added authentication functionality, including secure user registration, login, and session management using bcrypt and cookies.

```typescript
app.post('/login', async (req, res) => {
    const { email, password } = req.body;
    const users = await sqlFetchUserByEmail(email);
    if (users.length > 0 && bcrypt.compareSync(password, users[0].password)) {
        const sessionId = await sqlCreateSession(users[0].id, new Date(Date.now() + 7 * 24 * 60 * 60 * 1000).toISOString());
        res.cookie('sid', sessionId, { signed: true, httpOnly: true, maxAge: 604800000 });
        res.status(200).json({ message: 'Login successful' });
    } else {
        res.status(401).json({ message: 'Invalid email or password' });
    }
});
```

### 2 Frontend Development with React

#### 2.1 Using React for Dynamic UI
I built dynamic and responsive user interfaces using React, including components for product display, search, and cart management.
```typescript
const fetchProduct = async (prodId: number) => {
    const productData = await singleProduct(prodId);
    setProduct(productData[0]);
};

useEffect(() => {
    fetchProduct(Number(id));
}, [id]);

return (
    <div className="productContainer">
        {product ? (
            <>
                <ImgDisplay imgurl={product.image_path} look="productImage" />
                <p>{product.name}</p>
                <p>{product.description}</p>
                <p>{`Price: $${product.price}`}</p>
            </>
        ) : (
            <p>Loading...</p>
        )}
        <Button btnText="Add to basket" btnonClick={() => handleAddToBasket(Number(id))} />
    </div>
);
```
 #### 2.2 State Management with Context API
 I implemented state management using the Context API to manage user state and shopping cart functionality.
```typescript
import { createContext, useContext, useState } from 'react';

type UserContext = {
    user: User | undefined;
    setUser: React.Dispatch<React.SetStateAction<User | undefined>>;
};

const UserContext = createContext<UserContext | null>(null);

export default function UserContextProvider({ children }: { children: React.ReactNode }) {
    const [user, setUser] = useState<User | undefined>(defaultUser);
    return <UserContext.Provider value={{ user, setUser }}>{children}</UserContext.Provider>;
}

export function useUserContext() {
    const context = useContext(UserContext);
    if (!context) throw new Error('useUserContext must be used within a UserContextProvider');
    return context;
}
```

#### 2.3 Implementing Search Functionality
Allows users to search for products by name or description and display the results.

```typescript
import { SearchProducts } from '../../utils/endpoints';

const handleSubmit = async (e: React.FormEvent<HTMLFormElement>) => {
    e.preventDefault();
    const response = await SearchProducts([userInput]);
    setUser({ ...user, search: response });
    setUserInput('');
};

return (
    <form onSubmit={handleSubmit}>
        <input value={userInput} onChange={(e) => setUserInput(e.target.value)} placeholder="Search" />
        <Button btnText="Search" />
    </form>
);
```

#### 2.4 Handling Product Reviews and Ratings
I fetched and displayed product reviews and ratings.
```typescript
const fetchProductReviews = async (prodId: number) => {
    const reviewsData = await fetchReviews(prodId);
    setReviews(reviewsData);
};

useEffect(() => {
    fetchProductReviews(Number(id));
}, [id]);

return (
    <div className="reviews">
        {reviews.map((review) => (
            <Review key={review.id} review={review} />
        ))}
    </div>
);
```

 ## Learning outcomes I have struggled with and/or would like to re-visit.
 
### 1 Handling Authentication and Authorization
Understanding session management and secure password handling with bcrypt was challenging. I revisited concepts of token-based vs. session-based authentication to solidify my understanding.

### 2 Managing Relational Data with SQLite
Designing a relational database schema and ensuring data integrity with foreign keys required careful planning and adjustments. I revisited concepts of one-to-many and many-to-many relationships to optimize the database design.

### 3 Integrating Frontend and Backend
Ensuring seamless communication between the React frontend and Express backend posed challenges, particularly with handling JSON data and API responses.

### 4 Handling CORS Issues
I encountered problems with CORS due to some computers resolving localhost as localhost and others as 127.0.0.1, causing inconsistent behavior. To resolve this, I removed the fixed IP in the CORS configuration, allowing for more flexibility and ensuring that the application worked consistently across different environments.


## Feedback (For CF's)
> [**Course Facilitator name**]  
> [*What went well*]  
> [*Even better if*]
