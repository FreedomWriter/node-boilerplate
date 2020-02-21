# Create models

- For each resource, create a directory (in the case of projects as a resource, create a directory named `projects`)
- In this folder, create a file named `projects-model.js`

  - require the db config
  - create needed functions and export them

- These are very simple models, you will likely need to perform a number of joins to return the data the front end needs

# Example of a model:

```javascript
const db =
  require("../data/db.config") -
  function find() {
    return db("projects").select();
  };

function findBy(filter) {
  return db("users")
    .where(filter)
    .findBy();
}

function findById(id) {
  return db("projects")
    .limit(req.query.limit)
    .orderBy("name", req.query.orderBy)
    .where({ id })
    .first();
}

async function add(project) {
  const [id] = await db("projects").insert(project);
  return db("projects")
    .where({ id })
    .first();
}

async function update(id, body) {
  await db("projects")
    .where({ id })
    .update(body);

  return findById(id);
}

function remove(id) {
  return db("projects")
    .where({ id })
    .del();
}

module.exports = {
  find,
  findBy,
  findById,
  add,
  update,
  remove
};
```

# An example of a more complex model using joins

```javascript
async function find() {
  const project = await db("projects as p")
    .leftJoin("values as v", "p.value_id", "v.id")
    .leftJoin("users as u", "u.id", "p.user_id")
    .leftJoin("user_data as ud", "ud.project_id", "p.id")
    .leftJoin("tasks as t", "t.project_id", "p.id")
    .leftJoin("project_resources as pr", "pr.project_id", "p.id")
    .leftJoin("resources as r", "r.id", "pr.resource_id")
    .leftJoin("task_contexts as tc", "tc.task_id", "t.id")
    .leftJoin("contexts as c", "c.id", "tc.context_id")
    .select(
      "p.user_id",
      "u.username",
      "p.id",
      "p.project_name",
      "p.project_description",
      "p.project_complete",
      "t.task_description",
      "t.task_notes",
      "t.task_complete",
      "r.resource_name",
      "r.resource_description",
      "c.context"
    );
  const valuesArr = await db("projects as p")
    .leftJoin("values as v", "p.value_id", "v.id")
    .select("v.value");
  const values = valuesArr.filter(value => value.value !== null);
  return { values, project };
}
```
