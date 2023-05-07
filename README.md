# Serverless aws middleware

This middleware provide the interface of lambda's handler to use `request => response`
and provide some AWS features like mysql, event queue, s3, logging, etc.

## Example usage

A wrapping function is provided from middleware

```ts
import {
  AWSPluginAux,
  LoggerPluginAux,
  LogLevel,
  middleware,
  MySQLPluginAux,
  TracerPluginAux,
} from 'serverless-simple-middleware';

export type Aux = AWSPluginAux &
  TracerPluginAux &
  LoggerPluginAux &
  MySQLPluginAux;

const dbConfiguration = {
  database: 'database name',
};

export const handler = middleware.build<Aux>([
  middleware.aws({
    config: undefined,
  }),
  middleware.trace({
    route: 'es:index/event',
    queueName: 'event_queue',
    system: 'AppName',
    awsConfig: undefined,
    region: 'ap-northeast-2',
  }),
  middleware.logger({
    name: __filename,
    level: LogLevel.Stupid,
  }),
  middleware.mysql({
    config: dbConfiguration,
  }),
]);
```

```ts
export const spam = handler(
  async ({ request, aux }): Promise<ResponseType> => {
    const { logger, db } = aux;
    const body: ReqestBody = request.body;
    logger.info('requset log');

    const response = await logic(db, body);
    return response;
  },
);
```

## Include features

- mysql
- event queue (AWS SQS)
- S3
- logging
- organize request object
- setting response object
