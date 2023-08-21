# Navigation
* ***[Project description](#project-description)***
* ***[Software version](#software-versions)***
* ***[How to start?](#how-to-start)***
* ***[Creating superuser](#creating-superuser)***
* ***[Using the api](#using-the-api)***
* ***[Database administration](#database-administration)***
* ***[Running black](#running-black)***
* ***[Running tests](#running-tests)***
  * ***[Running tests inside the container](#running-tests-inside-the-container)***
  * ***[Running tests locally](#running-tests-locally)***
* ***[License](#license)***

# Project description

The project is an admin panel for a web application named "Attendance Journal," designed to enhance the organizational efficiency of teachers and methodists. It offers a RESTful API, encompassing features like authentication, registration, user management (viewing, modifying, deleting, blocking) within the journal. Additionally, it involves role-based user division (administrators, teachers, and methodists) and the synchronization of dual databases. The designated functionalities are accessible exclusively to a designated user-administrator.

This service functions as a backend, providing a RESTful API. Its comprehensive features span across authentication and authorization mechanisms, user segmentation based on specific roles, as well as the management of personal and CRM data. Simultaneously, the service interacts with multiple databases, with PostgreSQL serving as the primary database and MSSQL functioning in a read-only mode. This dual database setup ensures a seamless transition to a new service. To handle real-time data requirements, Redis comes into play.

The API, along with its underlying business logic, is realized through a robust tech stack composed of Django, DRF, and Spectacular. The dynamic duo of Django ORM and SqlAlchemy facilitate database operations, while APScheduler manages periodic tasks. For testing, the service harnesses the power of pytest, enabling a structured approach with custom fixtures and scenario coverage. The result is a clean environment post-testing.

The codebase stands as a testament to quality, undergoing linter checks and type validation by mypy. The codebase adheres to a consistent style and architectural design influenced by Django's paradigms. Furthermore, the service is containerized within a Docker image. Both the Dockerfile and docker-compose.yml were developed and maintained, with the project adhering to the 12-factor principles. Versioning, environment variable configurations, and health checks for all components ensure a robust deployment.

To enhance user experience, the project boasts automated documentation via Swagger and a comprehensive README guide. This guide not only aids in using the service but also provides insights into future development and deployment processes. Ultimately, the project embodies a holistic approach to enhancing administrative processes through technological prowess.


# Software versions

- PYTHON: ***3.11.3***
- PIP: ***23.1.2***
- POETRY: ***1.5.1***
- DJANGO: ***4.2.1***
- DJANGO REST FRAMEWORK: ***3.14.0***

# How to start?
 1. Install [Docker](https://docs.docker.com/engine/install/) on your local machine, if it wasn't done yet, and launch it;
 2. Clone current repository to your local machine;
 ```
 https://github.com/LeatherDiamond/attendance-journal-backend.git
 ```
 3. Navigate to the root directory of the project;
 4. Configure `.env` file by assigning values to the variables defined in `.env.sample`;
 5. In the project directory, run `docker-compose up --build` to start the services;

 > ###### **NOTE:** 
 > *Variables values provided in `.env.sample` for MSSQL should be changed in `.env` file in future for correct synchronization of your application with internal CRM system.*

After completing all the steps, the project will be launched and available at `http://localhost:8000/`.

# Creating superuser

If you already followed the **[How to start](#how-to-start)** section steps, superuser will be automatically created in webapp container with the credentials provided in `.env` variables `DJANGO_SUPERUSER_USERNAME` and `DJANGO_SUPERUSER_PASSWORD`.

This superuser can be used to get access to the django admin panel while the project is launched in containers.
If you want to create superuser locally you can also see how to do it in  **[Running tests](#running-tests)** section below.

# Using the API

The project provides full api documentation in the following endpoint: `api/docs/`.
Make GET request to this endpoint to view the entire project's API schema.

# Database administration

To administer the `PostgreSQL` database, you can use pgAdmin, which runs in a separate container. To access pgAdmin, follow these steps:

1. Open a browser and go to `http://localhost:5050/`;
2. Enter the login and password from your `.env` file to log in to pgAdmin;
3. Create a new connection to the necessary database;
4. Connect to the database and perform the necessary operations;


# Running black

To check and run code reformation you should navigate to the root directory and ensure that virtual environment is activated:

1. Run `poetry shell` to activate environment if it's not active yet;
2. Run `black . --check` to check if the code needs to be reformatted;
3. Run `black .` to reformat the code;


# Running tests
 
> ###### NOTE:
> **Before running tests, your mssql settings in `.env` file should be configured in a special way (cause your mssql variables in env file are used for synchronization with CRM system, thats why for tests you should choose your own database). Example of settings you can find below:**
```
MSSQL_DATABASE_USERNAME=sa
MSSQL_DATABASE_PASSWORD=6PvqdaM11D #The password must be at least 8 characters long and contain characters from three of the following four sets: Uppercase letters, Lowercase letters, Base 10 digits, and Symbols.
MSSQL_DATABASE_NAME=master
# Use the following value if you are going to start tests inside the webapp container.
MSSQL_DATABASE_HOST=journal.mssql
# Use the following value if you are going to start tests with application launched on local machine.
MSSQL_DATABASE_HOST=localhost
```
> **Master database will be by default in SQL Server that is running inside the container. We can use it for tests, but you can create another database and use it for tests, if it's neccessary.**

# Running tests inside the container:
1. Make sure that points 1-4 from ***[How to start](#how-to-start)*** section are already completed;
2. Configure MSSQL settings in `.env` file as mentioned above;
3. Build images and run containers:
```
docker compose up --build
```
4. As soon as all containers started, enter the webapp container:
```
docker exec -it journal.webapp bash
```
5. After you entered the webapp container, launch tests:
```
pytest
``` 

# Running tests locally:
1. Make sure that repository is already cloned on your computer, docker is launched, images already built and are ready for use and containers are not started yet;
2. Navigate to the root derictory of the project;
3. Configure `.env` file by assigning values to the variables defined in `.env.sample`;
 > ###### **NOTE:**
 > Use MSSQL settings mentioned above and additionally set in **PRIMARY_DATABASE_URL** configuration for your local database (for example PRIMARY_DATABASE_URL=postgres://admin:admin@localhost:5432/testbase)
4. Activate virtual environment:
```
poetry shell
```
5. Install all dependencies:
```
poetry install
```
6. Launch mssql container for future tests:
```
docker compose up mssql_db
```
7. Apply all migrations:
```
python project/manage.py migrate
```
8. Create default superuser in your database with credentials provided in your `.env` file:
```
python project/create_default_db_data.py
```
9. Launch the server:
```
python project/manage.py runserver
```
10. Start tests with the following command in console:
```
pytest
```
> ###### **NOTE:**
> If you will try to put the command in another console, make sure that virtual environment is active - *point 4* of the current instruction.

# License

This project is licensed under the MIT License - see the [LICENSE](https://github.com/LeatherDiamond/attendance-journal-backend/blob/main/LICENSE) file for details.
