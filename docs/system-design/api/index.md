# API Design and Develop Cheatsheet

## Restful API Design and Develop Guidelines

### Restful API Design criteria

#### Database Design Criteria

- [x] Data size
- [x] Reads & Writes
- [x] Number of operations
- [x] Operations per second
- [x] Required latency
- [x] Pinning attribute queries
- [x] Durability

**Identify Workload**

- [Identify Workload Template Google Sheet Version](https://docs.google.com/spreadsheets/d/1ggt0ZZD1UXqrz7Jw771f4OA3YhEwp00l1JHFHnQpioM/edit?usp=sharing)

### Basic steps to design and develop a Restful API

#### 1. High Level Design

- Break system into components/modules

#### 2. Designing and Seeding the Database

- Read and analyze requirements, define system actors: entity + action + relationship to create your database schema
- Design your data constraints: data types, data size, value restrictions
- Optimize the basic actions: Read, Find with/without Filter, Insert, Update, Delete, Bulk Actions: Insert, Update, Delete
- Make decision to choose DB Engine
- Insert your seeding data
- Test the output schema

#### 3. API Design Usecases

![API Design Usecases](/assets/api/api-design-usecases-template.png)

- [Usecases API Design Template](https://docs.google.com/spreadsheets/d/1h_PBtAXbMbPNh0qw-Jk1ex9zWqn1LJOJWSFn1XkTeu8/edit?usp=sharing)
