# Deployment Notes

## Qoute

> Developers get paid to deliver value, not lines of code.
>
> &mdash; A pragmatic developer

## Jargon

_"Single Responsiblity Principle"_

- a single unit of work should do one thing, one thing only, and do it really well. (It should only have one reason to change)

## Deployment

- extract configuration into enviroment variables
- set up Continuos Deployment from GitHub to Heroku

# Steps For Continous Deployment

1. Add start script
   "start": "node index.js"
   start is one of the few keywords that you don't have to use "run" with.

2. Add dotenv as a dependency `npm i dotenv`

   - You can add an environment variable by typing

   `export PORT=4000`

   in the terminal (to remove `unset PORT`). This is an alternative to dotenv

   - `const dotenv = require('dotenv').config()` at the highest level in index.js (assuming that's where your server is listening)
   - At the root, create a file named `.env`
   - Hide your config data: in your gitignore file include `.env`

3. In Heroku
   - create a new app
   - connect to github repo
   - select continous deployment

## Misc

- [The Twelve-Factor App](https://12factor.net/)

- env variables
