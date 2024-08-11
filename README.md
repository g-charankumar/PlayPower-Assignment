# Play Power Labs Assignments Reference Document

- Frontend Assignment
    
    ### Step 1: Set Up Your React Environment
    
    First, create a new React application using Create React App if you haven’t already set up a project. Open your terminal and run:
    
    ```bash
    npx create-react-app timezone-converter
    cd timezone-converter
    npm start
    ```
    
    This will set up a basic React environment and start your development server.
    
    ### Step 2: Install Necessary Packages
    
    You might need a few additional packages like `moment-timezone` for handling time zones and `react-datepicker` for a date picker component:
    
    ```bash
    npm install moment-timezone react-datepicker
    ```
    
    For drag-and-drop functionality (to reorder time zones), you can use `react-beautiful-dnd`:
    
    ```bash
    npm install react-beautiful-dnd
    ```
    
    ### Step 3: Create Basic Components
    
    1. **Time Zone Converter Component**:
        - This will be the main component where users interact with the time conversion.
        - Include a slider to adjust time and display the corresponding time in different time zones.
    2. **Time Zone Display Component**:
        - A smaller component to render each time zone's current time.
        - Include a button to remove the time zone.
    3. **Add Time Zone Component**:
        - Allows users to add new time zones to the list.
    
    ### Step 4: Building the Main Component (`TimeZoneConverter`)
    
    In your main component, manage the list of time zones and the current selected time using React's state.
    
    ```jsx
    import React, { Component } from 'react';
    import moment from 'moment-timezone';
    import { DragDropContext, Droppable, Draggable } from 'react-beautiful-dnd';
    
    class TimeZoneConverter extends Component {
      constructor(props) {
        super(props);
        this.state = {
          timeZones: ['UTC', 'Asia/Kolkata'], // Default time zones
          currentTime: moment(),
        };
      }
    
      handleTimeZoneAddition = (timeZone) => {
        this.setState(prevState => ({
          timeZones: [...prevState.timeZones, timeZone]
        }));
      };
    
      handleTimeZoneDeletion = (index) => {
        this.setState(prevState => ({
          timeZones: prevState.timeZones.filter((_, i) => i !== index)
        }));
      };
    
      onDragEnd = (result) => {
        if (!result.destination) return;
        const items = Array.from(this.state.timeZones);
        const [reorderedItem] = items.splice(result.source.index, 1);
        items.splice(result.destination.index, 0, reorderedItem);
        this.setState({ timeZones: items });
      };
    
      render() {
        const { timeZones, currentTime } = this.state;
        return (
          <DragDropContext onDragEnd={this.onDragEnd}>
            <Droppable droppableId="droppable">
              {(provided, snapshot) => (
                <div {...provided.droppableProps} ref={provided.innerRef}>
                  {timeZones.map((zone, index) => (
                    <Draggable key={zone} draggableId={zone} index={index}>
                      {(provided, snapshot) => (
                        <div
                          ref={provided.innerRef}
                          {...provided.draggableProps}
                          {...provided.dragHandleProps}
                        >
                          {/* Render each time zone here */}
                          <TimeZoneDisplay
                            timeZone={zone}
                            currentTime={currentTime}
                            onDelete={() => this.handleTimeZoneDeletion(index)}
                          />
                        </div>
                      )}
                    </Draggable>
                  ))}
                  {provided.placeholder}
                </div>
              )}
            </Droppable>
          </DragDropContext>
        );
      }
    }
    ```
    
    ### Step 5: Implement Additional Functionalities
    
    Add components and functionality for:
    
    - Date picking to adjust the current time (`react-datepicker`).
    - A button to reverse the order of the time zones.
    - A toggle for dark mode (use CSS for styling changes).
    - Shareable link generation (use state to manage and display URLs).
    - A `Schedule Meet` button that opens Google Calendar with a preset event.
    
    ### Step 6: Testing and Deployment
    
    Ensure your application works as intended:
    
    - Test all functionalities manually.
    - Write simple unit tests if possible (using Jest and Enzyme or React Testing Library).
    
    Finally, deploy your application using Netlify or similar platforms. Follow the respective platform’s documentation for deployment instructions.
    
    ### Step 7: Submission
    
    Prepare your submission as per the assignment's guidelines:
    
    - Zip your code excluding `node_modules`.
    - Include a hosted URL if you have one.
    - Record a demonstration of your application.
    
- Backend Assignment
    
    ### Step 1: Setting Up Your Node.js Project
    
    ### 1.1 Initialize Your Project
    
    - Open your terminal.
    - Create a new directory: `mkdir my_project` and navigate into it: `cd my_project`.
    - Initialize a new Node.js project: `npm init -y`.
    
    ### 1.2 Install Dependencies
    
    - Install Express for the web server: `npm install express`.
    - Install SQLite for the database: `npm install sqlite3`.
    - Install JSON Web Tokens for authentication: `npm install jsonwebtoken`.
    - Install dotenv for environment variables: `npm install dotenv`.
    
    ### 1.3 Directory Structure
    
    Organize your project files:
    
    ```
    my_project/
    │
    ├── src/
    │   ├── routes/
    │   │   ├── auth.js
    │   │   └── assignments.js
    │   ├── models/
    │   │   └── database.js
    │   ├── middleware/
    │   │   └── authenticateToken.js
    │   └── app.js
    │
    ├── db/
    │   └── sqlite.db
    │
    ├── .env
    ├── Dockerfile
    ├── package.json
    └── README.md
    ```
    
    ### Step 2: Building the Application
    
    ### 2.1 Create the Express Server (`src/app.js`)
    
    ```jsx
    const express = require('express');
    const app = express();
    app.use(express.json()); // Middleware to parse JSON bodies
    
    // Import routes
    const authRoutes = require('./routes/auth');
    const assignmentsRoutes = require('./routes/assignments');
    
    // Use routes
    app.use('/api/auth', authRoutes);
    app.use('/api/assignments', assignmentsRoutes);
    
    const PORT = process.env.PORT || 3000;
    app.listen(PORT, () => console.log(`Server running on port ${PORT}`));
    ```
    
    ### 2.2 Define Routes and Logic
    
    - **Authentication Routes (`src/routes/auth.js`)**: Handle login and issue JWTs.
    - **Assignments Routes (`src/routes/assignments.js`)**: Implement CRUD operations for assignments.
    - **Database Setup (`src/models/database.js`)**: Manage SQLite database connections and schema.
    
    ### Step 3: Dockerize Your Application
    
    ### 3.1 Write a Dockerfile
    
    This file instructs Docker on how to build your application image.
    
    ```docker
    # Use the official Node.js 14 image as a parent image
    FROM node:14
    
    # Set the working directory inside the container
    WORKDIR /app
    
    # Copy package.json and package-lock.json (or npm-shrinkwrap.json) files
    COPY package*.json ./
    
    # Install dependencies inside the container
    RUN npm install
    
    # Copy the rest of your application's source code from your host to your image filesystem.
    COPY . .
    
    # Make port 3000 available to the world outside this container
    EXPOSE 3000
    
    # Define the command to run your app using CMD which defines your runtime
    CMD ["node", "src/app.js"]
    ```
    
    ### 3.2 Build the Docker Image
    
    From your project directory (where your Dockerfile is), run:
    
    ```bash
    docker build -t my_microservice .
    ```
    
    This command builds a new Docker image locally, tagging it as `my_microservice`.
    
    ### 3.3 Run Your Docker Container
    
    After the image is built, run it with:
    
    ```bash
    docker run -p 3000:3000 my_microservice
    ```
    
    This command runs your Docker container, mapping port 3000 of the container to port 3000 on your host, allowing you to access the application via `localhost:3000`.
    
    ### Step 4: Prepare Documentation and Submission
    
    - **API Documentation**: Use tools like Swagger or manually create a document outlining your API endpoints, methods, and expected payloads.
    - **README File**: Document how to set up and run your application, including Docker commands.
    - **Prepare for Submission**: Follow the guidelines provided in your assignment for submitting your project, typically via email with a link to download your project files.
