# Data Persistance using PostgreSQL & TypeORM

A common use case for a backend is to read and write data in a database. For this, TypeORM is a powerful tool that allows us to easily manage our database.

## Type ORM

- ORM stands for Object Relational Mapping
- TypeORM is an ORM that can run in NodeJS, it's most commonly used with Typescript
- It can be used with many different databases

### Installation

To install typeORM and the postgres driver, run the following command:

```bash
yarn add typeorm @nestjs/typeorm pg
```

## Entities

- An entity is a class that maps to a database table
- Each *instance* of an entity represents a **row** in the table
- Each *property* of an entity represents a **column** in the table
- When working with NestJS and a database, we usually ditch model files and use entities instead

### Creating an Entity

To create an entity, we create a class and decorate it with the `@Entity()` decorator. We also need to specify the name of the table that this entity maps to. We can do this by passing the table name as an argument to the decorator.

```ts
import { Entity } from 'typeorm';
import { TaskStatus } from './task-status.enum';

@Entity() // This is a decorator, tells that this is a database entity
export class Task {
  @PrimaryGeneratedColumn('uuid') // Autogenerate the id, and treat it as the primary key
  id: string;

  @Column() 
  title: string;

  @Column()
  description: string;

  @Column()
  status: TaskStatus;
}
```

## ORM Design Patterns

There's two main design patterns that we can use when working with an ORM:

- Active Record Pattern
- Data Mapper Pattern

TypeORM supports both patterns, but there's some considerations to be made when choosing one or the other.

### Active Record Pattern

- All query methods are defined inside the entity class
- The developer interacts with the Entity class directly for queries

  👍

  - Fast to implement, better for small projects

  👎

  - Becomes messy quickly, not scalable

### Data Mapper Pattern

- Entities only have attributes and pure functions (which don't interact with the database)
- A `@Repository` class is created for query entity logic

  👍
  - Better for large projects, far more scalable and clean

  👎

  - Takes longer to implement
  - More boilerplate code

### Creating a Data Mapper Repository in NestJS

To create a repository in the latest version of NestJS, we need to create a class which extends the `Repository` class from TypeORM. We also need to pass the entity class as a generic type argument. We also need to decorate the class with the `@Injectable()` decorator.

If we wish to add custom methods to our repository, we can do so by creating a class method. We must also modify our `tasks.module.ts` file (or whatever module we're injecting the repository into) to inject the repository into the module.

tasks.module.ts

```ts
import { Module } from '@nestjs/common';
import { TasksController } from './tasks.controller';
import { TasksService } from './tasks.service';
import { TypeOrmModule } from '@nestjs/typeorm';
import { TasksRepository } from './tasks.repository';
import { Task } from './data.entity';

@Module({
  imports: [TypeOrmModule.forFeature([Task])], // Inject the typeORM module and specify the entities
  controllers: [TasksController],
  providers: [TasksService, TasksRepository], // Inject the repository into the module
})
export class TasksModule {}
```

We should also modify our service to inject the repository into the service.

tasks.service.ts

```ts
@Injectable()
export class TasksService {
  constructor(private readonly tasksRepository: TasksRepository) {} // Inject the repository into the service constructor

    getTasks(filterDto: GetTasksFilterDto): Promise<Task[]> {
    return this.tasksRepository.getTasks(filterDto); // Use the repository custom method
  }

    // ...
}
```

tasks.repository.ts

```ts
@Injectable()
export class TasksRepository extends Repository<Task> {
  constructor(private dataSource: DataSource) {
    super(Task, dataSource.createEntityManager());
  }

    async getTasks(filterDto: GetTasksFilterDto): Promise<Task[]> {
    const { status, search } = filterDto;

    const query = this.createQueryBuilder(
      'task', // How we want to refer to our entity in the query
    );

    if (status) {
      query.andWhere(
        'task.status = :status', // Map the parameter
        { status: status }, // Map the value
      );
    }

    if (search) {
      query.andWhere(
        'LOWER(task.title) LIKE LOWER(:search) OR LOWER(task.description) LIKE LOWER(:search)', // Map the parameter
        { search: `%${search}%` }, // Map the value
      );
    }

    const tasks = await query.getMany(); // Execute the query

    return tasks;
  }
}
```
