openapi: 3.0.0     
info:
  title: LMS
  description: REST APIs for managing trainers and their courses
  version: 1.1.0

servers:
  - url: http://localhost:8080
    description: Local Development Server

paths:
  /api/trainers/register:
    post:
      summary: Register a new trainer
      tags: [Trainers]
      description: Creates a new trainer profile with login credentials and expertise details.
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/TrainerRegisterRequest'
      responses:
        '201':
          description: Trainer account created successfully
          content:
            application/json:
              schema:
                type: object
                properties:
                  message:
                    type: string
                    example: "Trainer registered successfully"
                  trainerId:
                    type: string
                    example: "T123"
        '400':
          description: Invalid request data
        '409':
          description: Trainer with this email already exists
        '500':
          description: Unexpected server error

  /api/trainers/login:
    post:
      summary: Trainer login
      tags: [Trainers]
      description: Authenticates a trainer using email and password, returning a JWT token on success.
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/TrainerLoginRequest'
      responses:
        '200':
          description: Trainer authenticated successfully
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/LoginResponse'
        '400':
          description: Missing or invalid input
        '401':
          description: Invalid email or password
        '500':
          description: Unexpected server error

  /api/trainers/{trainerId}/courses:
    post:
      summary: Add a new course
      tags: [Courses]
      description: Allows a trainer to create and publish a new course under their profile.
      parameters:
        - in: path
          name: trainerId
          required: true
          schema:
            type: string
          description: Unique identifier of the trainer
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/CourseRequest'
      responses:
        '201':
          description: Course created successfully
          content:
            application/json:
              schema:
                type: object
                properties:
                  message:
                    type: string
                    example: "Course added successfully"
                  courseId:
                    type: string
                    example: "C101"
        '400':
          description: Invalid course data
        '404':
          description: Trainer not found
        '409':
          description: Course already exists
        '500':
          description: Unexpected server error

    get:
      summary: Get all courses by trainer
      tags: [Courses]
      description: Retrieves all courses that belong to the specified trainer.
      parameters:
        - in: path
          name: trainerId
          required: true
          schema:
            type: string
          description: Unique identifier of the trainer
      responses:
        '200':
          description: List of courses created by the trainer
          content:
            application/json:
              schema:
                type: array
                items:
                  $ref: '#/components/schemas/CourseResponse'
        '400':
          description: Invalid trainer ID format
        '404':
          description: Trainer not found
        '500':
          description: Unexpected server error

  /api/courses/{courseId}:
    put:
      summary: Update course details
      tags: [Courses]
      description: Updates an existing course with new details such as duration, price, or status.
      parameters:
        - in: path
          name: courseId
          required: true
          schema:
            type: string
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/CourseRequest'
      responses:
        '200':
          description: Course updated successfully
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/CourseResponse'
        '404':
          description: Course not found
        '500':
          description: Unexpected server error

    delete:
      summary: Delete a course
      tags: [Courses]
      description: Removes a course permanently from the system using its unique ID.
      parameters:
        - in: path
          name: courseId
          required: true
          schema:
            type: string
      responses:
        '200':
          description: Course deleted successfully
          content:
            application/json:
              schema:
                type: object
                properties:
                  message:
                    type: string
                    example: "Course deleted successfully"
        '404':
          description: Course not found
        '500':
          description: Unexpected server error

components:
  schemas:
    TrainerRegisterRequest:
      type: object
      description: Trainer details required for account creation
      properties:
        trainerId:
          type: string
          example: "HG"
        name:
          type: string
          example: "Anusha"
        email:
          type: string
          format: email
          example: "anusha@lms.com"
        expertise:
          type: string
          example: "Java, Spring Boot, Spring Security"
        password:
          type: string
          format: password
          example: "ddfsAfdfs@123"
        createdAt:
          type: string
          format: date-time
          example: "2025-09-16T10:00:00Z"
      required: [name, email, password]

    TrainerLoginRequest:
      type: object
      description: Credentials for trainer login
      properties:
        email:
          type: string
          format: email
          example: "anu@lms.com"
        password:
          type: string
          format: password
          example: "anulms@123"
      required: [email, password]

    LoginResponse:
      type: object
      description: Token information returned after successful login
      properties:
        token:
          type: string
          example: "dhusgddraesfdt"
        expiresIn:
          type: integer
          example: 3600
        trainerId:
          type: string
          example: "HG"

    CourseRequest:
      type: object
      description: Information required to create or update a course
      properties:
        courseId:
          type: string
          example: "C101"
        courseName:
          type: string
          example: "Mobile Development"
        category:
          type: string
          example: "Software Development"
        duration:
          type: string
          example: "6 weeks"
        price:
          type: number
          example: 499.99
        status:
          type: string
          enum: [ACTIVE, INACTIVE]
          example: "ACTIVE"
      required: [courseId, courseName, category]

    CourseResponse:
      type: object
      description: Details of a course returned by the system
      properties:
        courseId:
          type: string
          example: "Sec1"
        courseName:
          type: string
          example: "Mobile Development"
        category:
          type: string
          example: "Software Development"
        duration:
          type: string
          example: "6 weeks"
        price:
          type: number
          example: 499.99
        status:
          type: string
          example: "ACTIVE"
        createdAt:
          type: string
          format: date-time
          example: "2025-09-16T10:00:00Z"
        updatedAt:
          type: string
          format: date-time
          example: "2025-09-17T12:00:00Z"
