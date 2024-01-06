# NestJs Main Concepts

## Modules

Modules are used to organize the application structure. They are used to group related components. They can be imported and exported to other modules. They can also be used to provide services to other modules.

### Creating a Module in NestJS

```ts
import { Module } from '@nestjs/common';
import { TasksController } from './tasks.controller';
import { TasksService } from './tasks.service';
import { TypeOrmModule } from '@nestjs/typeorm';
import { TasksRepository } from './tasks.repository';
import { Task } from './data.entity';

@Module({
  controllers: [TasksController], // Specify the controllers
  providers: [TasksService], // Specify the services
})
export class TasksModule {}
```

## Controllers

Controllers are responsible for handling incoming requests and returning responses to the client. They can have `providers` methods injected into them, which are responsible for handling the business logic of the application. Controllers are also responsible for handling the routing of the application.

### Creating a Controller in NestJS

```ts
import { Controller, Get } from '@nestjs/common';
@Controller('tasks')
export class TasksController {
  constructor(private tasksService: TasksService) {} // Inject the service into the controller constructor

  @Get() // Example of a GET request
  getTasks(@Query() filterDto: GetTasksFilterDto): Promise<Task[]> {
    return this.tasksService.getTasks(filterDto);
  }

  @Get('/:id') // Example of a GET request with a parameter
  getTaskById(@Param('id') id: string): Promise<Task> {
    return this.tasksService.getTaskById(id);
  }

  @Post() // Example of a POST request with a body
  createTask(@Body() createTaskDto: CreateTaskDto): Promise<Task> {
    console.log('Body: ', createTaskDto);
    return this.tasksService.createTask(createTaskDto);
  }
}
```

As you may have noticed in the example above, we can use decorators to specify the type of request we want to handle. We can also specify the route we want to handle. In this case, we are handling the `/tasks` route. We can also specify the type of data we want to receive in the request body, query parameters, and route parameters.

We also used dependency injection to inject the service into the controller constructor. This allows us to use the service methods in the controller. Services and DTOs are explained in the next section.

## Providers (Services)

Providers are responsible for handling the business logic of the application.

- They can be injected into constructors
- They could be a value, method, etc
- They must be provided to a module to be usable
- Providers can also be exported by a module for other modules to use

### Services

Services are a type of provider. They can act as a `Singleton` when wrapped with the `@Injectable()` decorator. This means that the service will be instantiated once and the same instance will be used throughout the application. This is the default behavior of services.

- A controller can have multiple services injected into it

### Creating a Service in NestJS

```ts
@Injectable()
export class TasksService {

  getTasks(filterDto: GetTasksFilterDto): Promise<Task[]> {
    // Logic for getting a task goes here
  }
}
```


## Data Transfer Objects (DTOs)

DTOs are used to define the shape of the data that is being sent to the server.

- 👍 Using DTO's makes it easier to maintain & refactor code!
- DTO's become a single source of truth, to avoid refactoring too much code
- They are not a class per se, just a plan Typescript `interface`  or `class`

### Creating a DTO in NestJS

create-task.dto.ts

```ts
import { IsNotEmpty } from 'class-validator';

export class CreateTaskDto {
  @IsNotEmpty()
  title: string;

  @IsNotEmpty()
  description: string;
}
```

## Pipes

Pipes operate on the arguments being passed to a controller method BBEFORE the method handler is executed. They are useful for:

1. Data transformation
2. Data validation

Pipes can:

- Throw exceptions
- Be async
- Go very well with DTOs

### Types of Pipes

1. Handler-scope pipes:
    - Defined within the handler method
    - Only available to that handler method
2. Parameter-scope pipes:
   - Only available to the parameter they are defined on
3. Global-scope pipes:
   - Available to the entire application
   - Defined in the `main.ts` file

### Creating a Global-scope Pipe in NestJS

main.ts

```ts
import { NestFactory } from '@nestjs/core';
import { AppModule } from './app.module';
import { ValidationPipe } from '@nestjs/common';

async function bootstrap() {
  const app = await NestFactory.create(AppModule);
  // Now whenever NestJS finds validation decorators, it will execute the according pipes
  // This saves us from a lot of code at the controller level
  app.useGlobalPipes(new ValidationPipe());

  await app.listen(3000);
}
bootstrap();
```

## Useful links

1. [NestJS Docs](https://docs.nestjs.com/)
