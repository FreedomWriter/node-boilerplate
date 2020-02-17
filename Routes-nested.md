# Nested Routes

## Parent Router

In the parent router we only need to require and use the child route

```javascript
const tasksRouter = require("../tasks/tasks.router");

const router = express.Router();

router.use("/:id/tasks", tasksRouter);
```

```javascript
const express = require("express");
const projectModel = require("./projects-model");
const db = require("./projects-model");
const tasksRouter = require("../tasks/tasks.router");

const router = express.Router();

router.use("/:id/tasks", tasksRouter);

router.get("/", async (req, res, next) => {
  try {
    const projects = await db.find();
    res.json(projects);
  } catch (err) {
    next(err);
  }
});

router.get("/:id", async (req, res, next) => {
  const { id } = req.params;
  const project = await db.findById(id);
  if (project) {
    res.json(project);
  } else {
    res.status(404).json({ message: "Could not find project with given id" });
  }
  try {
  } catch (err) {
    next(err);
  }
});

router.post("/", async (req, res, next) => {
  try {
    const newproject = await projectModel.add(req.body);
    res.status(201).json(newproject);
  } catch (err) {
    next(err);
  }
});

router.put("/:id", async (req, res, next) => {
  try {
    const { id } = req.params;
    const project = await projectModel.update(id, req.body);
    if (project) {
      res.json(project);
    } else {
      res.status(404).json({ message: "Could not find project with given id" });
    }
  } catch (err) {
    next(err);
  }
});

router.delete("/:id", async (req, res, next) => {
  try {
    const { id } = req.params;
    const deletedCount = await projectModel.remove(id);
    console.log(deletedCount);
    if (deletedCount) {
      res.json({ removed: deletedCount });
    } else {
      res.status(404).json({ message: "Could not find project with given id" });
    }
  } catch (err) {
    next(err);
  }
});

module.exports = router;
```

## Child Router

In the child route, we only need to merge params to have access to the parent routes params, if the child has an `id` that needs to be pulled from params, be careful not to call it `id` as that is likely being used in the parent param. Instead do something like:

```javascript
router.post("/:taskID", async (req, res, next) => {
  try {
    const { id } = req.params;
    const newTask = await db.add(id, req.body);

    res.json(newTask);
  } catch (err) {
    next(err);
  }
});
```

```javascript
const express = require("express");
const db = require("./tasks-models");

const router = express.Router({
  mergeParams: true
});

router.get("/", async (req, res, next) => {
  try {
    const { id } = req.params;
    const tasks = await db.find(id);

    res.json(tasks);
  } catch (err) {
    next(err);
  }
});

router.post("/", async (req, res, next) => {
  try {
    const { id } = req.params;
    const newTask = await db.add(id, req.body);

    res.json(newTask);
  } catch (err) {
    next(err);
  }
});

module.exports = router;
```
