## Guidance
Answer the following questions considering the learning outcomes for
- [Week 1](https://learn.foundersandcoders.com/course/syllabus/developer/project-1-server/learning-outcomes/)
- [Week 2](https://learn.foundersandcoders.com/course/syllabus/developer/project-1-frontend/learning-outcomes/)
- [Week 3](https://learn.foundersandcoders.com/course/syllabus/developer/project-1-test-deploy/learning-outcomes/)

Make sure to record evidence of your processes. You can use code snippets, screenshots or any other material to support your answers.

Do not fill in the feedback section. The Founders and Coders team will update this with feedback on your progress.

## Assessment
 ### 1. Show evidence of some of the learning outcomes you have achieved this week.
#### Develop a server using Express and TypeScript for backend applications (K9, S1, S11, S12)
 
  I set up the server using Express and TypeScript, including environment variables and route handlers.
 ```tsx
 import express, { Express } from 'express';
 import dotenv from 'dotenv';
 import morgan from 'morgan';
 import { POSThandler, FILTERhandler, ALLhandler } from './routeHandlers';
 
 dotenv.config();
 const app: Express = express();
 const port = process.env.PORT || 3000;
 
 app.use(morgan('dev'));
 app.use(express.json());
 
 [POSThandler, FILTERhandler, ALLhandler].forEach(handler => handler(app));
 
 app.listen(port, () => {
     console.log(`[server]: Server is running at ${port}`);
 });
 ```

#### Identify the server’s role within a full stack application (K1, K2, K3)
  
   The server is responsible for handling client requests, processing them, and responding with the appropriate data. It interacts with the database and serves as a bridge between the frontend and the backend.

#### Understand the benefits of using TypeScript over JavaScript, such as improved code quality, enhanced developer productivity, and better tooling support (K7, S1)

  TypeScript provided type safety and better tooling support, helping catch errors early and improving productivity. Defining the Question type ensured data consistency.

```tsx
export type Difficulty = 'easy' | 'medium' | 'hard';

export type Question = {
  id: number;
  category: string;
  difficulty: Difficulty;
  question: string;
  options: string[];
  answer: string;
  favourited: boolean;
  timestamp: Date;
};
```
#### Grasp the concept and purpose of RESTful APIs (K11, S12)
RESTful APIs allow interaction with the server using standard HTTP methods.

#### Construct and implement RESTful APIs effectively (S1, S11, S12, S16)

Implemented various RESTful API endpoints for creating, retrieving, updating, and deleting quiz questions.

```tsx
import { Request, Response } from 'express';

app.post('/questions', (req: Request, res: Response) => {
  const newQuestion = req.body;
  // Logic to add the question to the database
  res.status(201).send(newQuestion);
});
```

#### Determine appropriate scenarios for applying RESTful APIs (K7, S9, B2)
RESTful APIs are used when there is a need to interact with the server to perform operations on resources, such as quiz questions in this application.

#### Design endpoint structures for data creation, modification, and deletion (K11, S8, S12)
Designed endpoints to handle various operations on quiz questions.
```tsx
app.put('/questions/:id', (req: Request, res: Response) => {
  const { id } = req.params;
  const updatedQuestion = req.body;
  // Logic to update the question in the database
  res.send(updatedQuestion);
});

app.delete('/questions/:id', (req: Request, res: Response) => {
  const { id } = req.params;
  // Logic to delete the question from the database
  res.status(204).send();
});
```
#### Utilize Postman to test RESTful APIs independently of frontend components, ensuring proper functionality, error handling, and adherence to API specifications (S5, S6, S7)

Used Postman to test the API endpoints, verifying their functionality and handling of different scenarios.



 ### 2. Show an example of some of the learning outcomes you have struggled with and/or would like to re-visit.
> [**Learning outcome...**]  
> [your evidence here]

## Feedback (For CF's)
> [**Course Facilitator name**]  
> [*What went well*]  
> [*Even better if*]
