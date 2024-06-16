## Guidance
Answer the following questions considering the learning outcomes for
- [Week 1](https://learn.foundersandcoders.com/course/syllabus/developer/project-1-server/learning-outcomes/)
- [Week 2](https://learn.foundersandcoders.com/course/syllabus/developer/project-1-frontend/learning-outcomes/)
- [Week 3](https://learn.foundersandcoders.com/course/syllabus/developer/project-1-test-deploy/learning-outcomes/)

Make sure to record evidence of your processes. You can use code snippets, screenshots or any other material to support your answers.

Do not fill in the feedback section. The Founders and Coders team will update this with feedback on your progress.

## Assessment
### 1. Show evidence of some of the learning outcomes you have achieved this week.
#### 1.1 Develop a server using Express and TypeScript for backend applications
 
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

#### 1.2 Identify the server’s role within a full stack application
  
   The server is responsible for handling client requests, processing them, and responding with the appropriate data. It interacts with the database and serves as a bridge between the frontend and the backend.

#### 1.3 Understand the benefits of using TypeScript over JavaScript, such as improved code quality, enhanced developer productivity, and better tooling support

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
#### 1.4 Grasp the concept and purpose of RESTful APIs
RESTful APIs allow interaction with the server using standard HTTP methods.

#### 1.5 Construct and implement RESTful APIs effectively

Implemented various RESTful API endpoints for creating, retrieving, updating, and deleting quiz questions.

```tsx
import { Request, Response } from 'express';

app.post('/questions', (req: Request, res: Response) => {
  const newQuestion = req.body;
  // Logic to add the question to the database
  res.status(201).send(newQuestion);
});
```

#### 1.6 Determine appropriate scenarios for applying RESTful APIs
RESTful APIs are used when there is a need to interact with the server to perform operations on resources, such as quiz questions in this application.

#### 1.7 Design endpoint structures for data creation, modification, and deletion
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
#### 1.8 Utilize Postman to test RESTful APIs independently of frontend components, ensuring proper functionality, error handling, and adherence to API specifications

Used Postman to test the API endpoints, verifying their functionality and handling of different scenarios.
![image](https://github.com/fac29/Alexander--progress-log/assets/94972293/7d4b7dcb-170e-453e-a63a-fab753fc89a1)

#### 1.8.2 Using Cypress for End-to-End testing
I utilized Cypress to perform end-to-end testing on our application. Below is an example of how I tested the edit question functionality.
```ts
describe('Edit question page', () => {
    it('Edit category on question 30', () => {
        cy.visit('http://localhost:5173/questionbank/editquestion/1');

        cy.get('[data-cy=categoryDropdown]').select('History');
        cy.get('[data-cy=editButton]').click();

        cy.visit('http://localhost:5173/questionbank/editquestion/1');
        cy.get('[data-cy=categoryDropdown] option:selected')
            .invoke('text')
            .should('eq', 'History');
    });
});

```


#### 1.9 Gain experience in documenting the application’s architecture, API endpoints, and user guides for future reference and maintenance

Documented the API endpoints and their usage in the README.md file for easy reference and maintenance. Additionally, used JSDoc comments to document React components for better clarity.

```tsx
/**
 * Creates a custom button.
 * @param {string} name - Text on the button. Optional
 * @param {React.ReactNode} preIcon - Icon on the left. Optional
 * @param {()=>void} handler - Button handler. Optional
 * @param {'blue' | 'red' | 'yellow' |'black'|'grey'|'green'|'orange'} color - Button color. Optional
 */
function Button({ name, preIcon, postIcon, handler, color = 'blue', 'data-cy': dataCy }: Props) {
```

#### 1.10 Utilize node for filesystem operations

Used node:fs to perform file operations, such as reading and writing data to a JSON file.

```tsx
import * as fs from 'fs/promises';

export async function loadData(): Promise<OuterQuestion> {
  const fileContent = await fs.readFile('data.json', 'utf8');
  return JSON.parse(fileContent);
}
```

#### 1.11 Set up HTTPS for secure communication
Configured HTTPS using SSL certificates for secure communication between the client and server.

```tsx
import https from 'https';
import * as fs from 'fs';
import path from 'path';

const keyPath = path.resolve(__dirname, '../keys/selfsigned.key');
const certPath = path.resolve(__dirname, '../keys/certs/selfsigned.crt');

if (fs.existsSync(keyPath) && fs.existsSync(certPath)) {
  const options = {
    key: fs.readFileSync(keyPath),
    cert: fs.readFileSync(certPath),
  };
  const serverHTTPS = https.createServer(options, app);
  serverHTTPS.listen(porthttps, () => {
    console.log(`[server]: HTTPS Server is running at https://localhost:${porthttps}`);
  });
}
```
Below are the instructions for creating the keys and configuring the EC2 instance to make the specific port available:

* Create folders on the root
```vsh
mkdir -p keys/certs
```

* Generate private key
```vsh
  openssl genpkey -algorithm RSA -out keys/selfsigned.key
```

* Create a certificate signing request (CSR)
```vsh
openssl req -new -key keys/selfsigned.key -out keys/selfsigned.csr
```

* Generate the self-signed certificate
```vsh
openssl x509 -req -in keys/selfsigned.csr -signkey keys/selfsigned.key -out keys/certs/selfsigned.crt -days 365
```


### 2. Show an example of some of the learning outcomes you have struggled with and/or would like to re-visit.

#### 2.1 Set up HTTPS for secure communication
Setting up HTTPS for secure communication was challenging, especially understanding the self-signed certificates part. We initially struggled with configuring the certificates correctly and making sure the server could run both HTTP and HTTPS. To simplify the solution, we decided to use two separate apps: one for HTTP and another for HTTPS. Additionally, we checked if the SSL key and certificate files exist locally and only then created the HTTPS server to avoid errors when using fs on a possible non-existent file.

```tsx
import https from 'https';
import * as fs from 'fs';
import path from 'path';
import express, { Express } from 'express';
import dotenv from 'dotenv';

dotenv.config();

const app: Express = express();
const apphttps: Express = express();
const port = process.env.PORT || 3000;
const porthttps = process.env.PORTHTTPS || 8443;

const keyPath = path.resolve(__dirname, '../keys/selfsigned.key');
const certPath = path.resolve(__dirname, '../keys/certs/selfsigned.crt');

if (fs.existsSync(keyPath) && fs.existsSync(certPath)) {
  const options = {
    key: fs.readFileSync(keyPath),
    cert: fs.readFileSync(certPath),
  };
  const serverHTTPS = https.createServer(options, apphttps);
  serverHTTPS.listen(porthttps, () => {
    console.log(`[server]: HTTPS Server is running at https://localhost:${porthttps}`);
  });
}

app.listen(port, () => {
  console.log(`[server]: Server is running at ${port}`);
});
```



## Feedback (For CF's)
> [**Course Facilitator name**]  
> [*What went well*]  
> [*Even better if*]
