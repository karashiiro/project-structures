# Blueprints module

## Project structure
`flask` can also be nested in some other module, such as `projectname.api.flask` or something.

```
app.py
|-projectname
| |-service1
| | \-...
| |-service2
| | \-...
| |-flask
| | |-__init__.py
| | |-config.py
| | |-routes1.py
| | |-routes2.py
| | \-...
| \-...
\-...
```

### Principles
`service1` and `service2` (and any additional services) should not reference Flask or `projectname.flask` in any form.

You may argue to take this principle further by nesting `service1` and `service2` under a `services` module, and never referencing parent modules.
Under this modified structure, you could accept `projectname.services.service1` importing `projectname.services.service2`, but not `projectname.services.service1`
importing a hypothetical `projectname.specialservice`. Modules importing siblings would be acceptable, but modules importing parents (or siblings of parents)
would not.

However, if we consider a module `projectname.common` with enumerations or types, `service1` and `service2` would not be able to access this module if
we follow our "siblings, but not parents" principle strictly. You may or may not find this acceptable or worthy of an exception.

### Routes
`routes1.py` and `routes2.py` each export a [Blueprint](https://flask.palletsprojects.com/en/2.0.x/tutorial/views/#create-a-blueprint), which is used to define
a number of routes contained within each respective file. Additional modules can be created which export additional blueprints containing routes or other callbacks.

### App configuration
`__init__.py` exports `configure(app: Flask)` and `scaffold(app: Flask)`. `configure(app)` sets configuration options on the `app` object and loads any
configuration files (`config.py` in this example). `scaffold(app)` registers the blueprints exported by `routes1.py` and `routes2.py` using
`app.register_blueprint(blueprint)`.

### App initialization
`app.py` defines the `app` object and calls `configure(app)` and `scaffold(app)`.

## Benefits
- Flask imports are limited to a single module, and don't need to be spread out through your entire project.
- Large modules can be broken down through the use of blueprints.
- No need to rely on import order to import a module with `app.route` decorators after instantiating the `app` object.
- The `app` object doesn't need to be imported by any other module.
  - You may still need to use `flask.current_app` in your routes, but at least your dependency graph will be tidy that way (so long as you follow the Principles).
- If you ever want to replace Flask, you only need to modify code in `app.py` and `projectname.api.flask`.
- Service unit tests don't need to consider Flask behaviors.
