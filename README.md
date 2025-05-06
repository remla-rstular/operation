# Operation of review sentiment system

Run the application with docker compose:

```
docker compose up -d
```

The service will be accessible at [http://localhost:8001](http://localhost:8001).

## Structure

### [App](https://github.com/remla-rstular/app)

The frontend application, powered by FastAPI that interacts with the backend service. It needs an API token (stored as a Docker secret) to authenticate against the backend. It will store corrections given by the users into a SQLite database.

Path to the backend application is configurable through the `MODEL_SERVICE_URL` variable. Path to the SQLite database is configurable through the `SQLITE_DB_PATH` variable. Listening port can be configured through the `PORT` variable.

### [Model service](https://github.com/remla-rstular/model-service)

The backend service that actually runs the model. Model is dynamically loaded at runtime, different versions can be selected by changing the `MODEL_VERSION` environment variable. Listening port can be configured through the `PORT` variable.

### [lib-version](https://github.com/remla-rstular/lib-version)

The versioning library is used to share data transfer objects (DTOs) between the app and the model service

### [lib-ml](https://github.com/remla-rstular/lib-ml)

Contains the pre-processing logic.

### [Model training](https://github.com/remla-rstular/model-training)

Used to train the model. Models are versioned, and published as releases to GitHub.
