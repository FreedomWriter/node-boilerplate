# Create Seeds

- seeds are not timestamped, create them with a prefix (00 for cleanup, the rest should start at 001 and increment to ensure that they run sequentially according to how they were created, as some data may be required in future tables they need to be run correctly. Add id's to seeds, it is not necessary but could avoid future issues with foreign keys expecting a particular id)

- npx knex seed:make

  - example of multiple seeds create sequentially exist [here](https://github.com/FreedomWriter/node-db-challenge/tree/master/data/seeds)

```javascript
exports.seed = async function(knex) {
  await knex("tasks").truncate();
  await knex("tasks").insert([
    { project_id: 1, task_description: "Enroll in Lambda" },
    { project_id: 1, task_description: "Cry", notes: "Don't cry too long" },
    { project_id: 1, task_description: "start understanding" }
  ]);
};
```

- When all seeds are created run them
  `npx knex:seed run`
