# Software testing
You'll find the written unit tests inside the files matching this pattern `*.spec.ts` 
In those files, you can distinguish the mocked services that we are testing, where we mock the functions exposed by the service to make them use fake calls to apis or database. Here is an example:
```typescript

    createTask: jest.fn(
      async (todoId: string, taskDto: CreateTaskDto): Promise<TaskEntity> => {
        const { name } = taskDto;
        const todo = await mockTodoRepository.findOne(todoId);
        const task: TaskEntity = await mockTaskRepository.create(
          todoId,
          taskDto,
        );
        await mockTaskRepository.save(task);
        return task;
      },
    ),
```
in the file [mocks.ts](./src/todo/test-artifacts/repositories/mocks.ts). You will find all of our repositories pattern mocks and fake data that we'll be using the unit tests.
the mocked repositories are objects that look like this:
```typescript
export const mockSomeRepository = {
  find: jest.fn(),
  findOne: jest.fn(),
  create: jest.fn(),
  delete: jest.fn(),
  update: jest.fn()
}
```
## End to End testing
In this section we'll create an instance of our backend and run tests on the API it exposes, then test the return HTTP_STATUS and payload against the expected values. Here is an example:
```typescript
  it('/api/todos (POST)', async () => {
    return request(app.getHttpServer())
      .post('/api/todos')
      .send({
        name: 'Newly Posted todo for Test purpose',
        description: 'Random description',
        userId: 'cafb1073-0ace-4dec-95e7-8f8934e0d019',
      })
      .expect(201)
      .then((response) => {
        const payload = response.body;
        delete payload.id;
        expect(payload).toStrictEqual({
          name: 'Newly Posted todo for Test purpose',
          description: 'Random description',
          owner: {
            id: 'cafb1073-0ace-4dec-95e7-8f8934e0d019',
            username: 'karim',
            email: 'karim@gmail.com',
          },
        });
      });
  });
```
## How to run the project:
1. Clone the repository, make sure you have docker and node 14+ installed
2. Run `docker-compose up`
3. run unit & integration tests by : `npm run test`
4. run e2e tests by: `npm run test:e2e`