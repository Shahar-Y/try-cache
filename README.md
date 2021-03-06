# try-cache

![npm](https://img.shields.io/npm/v/try-cache?color=green)
![NPM](https://img.shields.io/npm/l/try-cache)
![Snyk Vulnerabilities for npm package](https://img.shields.io/snyk/vulnerabilities/npm/try-cache)
![npm](https://img.shields.io/npm/dt/try-cache)

An auto-caching npm package for super-fast retrieval of data that doesn't have to be atomically consistant,
allowing automatic cache update and expiration according to the user's needs.

## Installation

`npm i --save try-cache`

## Options

### Initiation options

```
/**
 * @param silent - if true, don't log anything to the console. Defaults to false.
 * @param expire - the default expiration time in seconds. Defaults to 5 mins.
 */
export type TCOptions = {
  silent: boolean;
  expire: number;
};
```

### Operation Options

```
/**
 * @param expire - the expiration time in seconds for the specific operation. Default to TCOptions' expire.
 * @param callbackFunction - the function to call if the retrieveFunction throws an error after cache failed. Defaults to "do nothing".
 * @param forceDB - if true, will force the retrieveFunction to be called even if the key is found in cache. Defaults to false.
 */
export type OperationOptions = {
  expire: number;
  callbackFunction: Function;
  forceDB: boolean;
};

```

## Usage Example

```
import { TryCache } from 'try-cache';

// Sets the expiration to 20 seconds, and alerts on calls
const cb = new TryCache('redis://localhost:6379', { silent: false, expire: 20 });

// Initiates the connection to redis
await cb.initTryCache();

// Should take 3 seconds
const res1 = await cb.tryCache('myKey', () => dummyDB(2, 7));
console.log('First time result:', res1);

// Should take a few ms because cahe is saved
const res2 = await cb.tryCache('myKey', () => dummyDB(2, 7));
console.log('Second time result:', res2);


// dummyDB simulates a database call
async function dummyDB(x: number, y: number) {
  await sleep(3000);

  return { x, y };
}
```

## To run the package locally with the example:

1. Run the docker-compose (or run redis locally manually): `docker-compose -f "src\miscellaneous\docker-compose.yml" up -d --build`
2. Run the example: `npm run example`

The output should show the different use-cases of the package (with and without cache at the beginning)

- Notice! The third and fourth runs should successfully fail as they simulate the failure of the original db retrieval function.
