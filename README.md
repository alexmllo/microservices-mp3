# Microservices MP3 Converter
This application converts video files to MP3 format using a microservices architecture.

- [Microservices MP3 Converter](#microservices-mp3-converter)
    - [Overview](#overview)
    - [Directory structure](#directory-structure)
    - [System Components](#system-components)
    - [Authentication Flow](#authentication-flow)

## Overview

1. __Authentication__:

    - Users must first authenticate by making a request to the API Gateway to obtain a JWT (JSON Web Token).
    - The JWT is used to authorize subsequent requests to other services.

2. __Video Upload__:

    - After obtaining a JWT, users upload a video file through the API Gateway.
    - The API Gateway stores the video in MongoDB and publishes a message to a RabbitMQ queue.

3. __Video Processing__:

    - A dedicated video-to-MP3 conversion service listens to the RabbitMQ queue.
    - Upon receiving a message, the service retrieves the video from MongoDB, converts it to MP3, and stores the MP3 file back in MongoDB.
    - After the conversion, it publishes a new message to the queue indicating that the MP3 file is ready.

4. __Notification__:

    - A notification service listens to the RabbitMQ queue for messages indicating completed conversions.
    - When it receives such a message, it sends an email notification to the user, informing them that their MP3 file is ready for download.

5. __MP3 Download__:

    - To download the MP3 file, the user must provide the ID received in the notification along with their JWT token.
    - The API Gateway verifies the JWT and uses the ID to fetch the MP3 file from MongoDB and serve it to the user.

## Directory Structure

The application is structured as follows:

```
/system_design
└── /python
    └── /src
        ├── /gateway          # API Gateway service
        ├── /auth             # Auth Service
        ├── /converter        # Video-to-MP3 Conversion Service
        ├── /notification     # Notification Service
        └── /storage          # MongoDB storage related utilities

```

## System Components

- __API Gateway__: Handles user requests, manages video storage, processes authentication, and facilitates MP7 downloads.
- __Auth Service__: Authenticates users and issues JWT tokens upon successful login.
- __Video-to-MP3 Conversion Service__: Processes videos to convert them into MP3 format.
- __Notification Service__: Sends email notifications to users when their MP3 file is ready.
- __MongoDB__: Stores video files, MP3 files, and relevant metadata.
- __RabbitMQ__: Facilitates communication between services through message queues.

## Authentication Flow
1. Request JWT: Users make a request to the API Gateway to authenticate and obtain a JWT.
2. Use JWT: The JWT is used to authenticate subsequent interactions with the API Gateway, including uploading videos and downloading MP3 files.