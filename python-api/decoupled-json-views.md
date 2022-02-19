# Decoupled JSON views
This isn't a project structure in itself, but rather a pattern that can be used in any other project structure. Python dictionaries can
generally be serialized to JSON, and web frameworks (such as Flask) allow for `dict` objects to be treated as JSON responses. This is
one way of strongly typing these responses.

## The thing
- [Pydantic](https://pydantic-docs.helpmanual.io/)
  - [Flask plugin](https://github.com/bauerji/flask-pydantic)
