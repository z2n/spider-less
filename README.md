# spider-less
Web spider on Serverless!

## About Spiderless

Spiderless is the backend layer of [KMPPP](https://kmppp.com), a web spider as a service application, it allows you to monitor and get notified of nearly anything on the web. It is built on top of these technologies:

| Technology  | Used For |
| ------------- | ------------- |
| [Bulma](https://bulma.io/), [Buefy](https://buefy.github.io/) | UI |
| [Vue.js](https://vuejs.org/) | Front-end logic |
| [AWS S3](https://aws.amazon.com/s3/) | Website hosting |
| [AWS Lambda](https://aws.amazon.com/lambda/) | Backend API |
| [AWS SNS](https://aws.amazon.com/sqs/) | Message queue |
| [AWS DynamoDB](https://aws.amazon.com/dynamodb/) | Database |
| [AWS API Gateway](https://aws.amazon.com/api-gateway/) | API gateway |
| [AWS Cloudfront](https://aws.amazon.com/cloudfront/) | CDN |
| [AWS Route 53](https://aws.amazon.com/route53/) | DNS |

## API Endpoints

### `GET` subscriptions

#### Description
Get a list of subscriptions (a maximum of 1 MB of data limited by DynamoDB).

#### Parameters
None

#### Request
```bash
curl /api/subscriptions
```

#### Response
```json
[
  {
    "createdAt": 1544833435070,
    "targets": [
      {
        "selector":"#title-overview-widget > div.vital > div.title_block > div > div.ratings_wrapper > div.imdbRating > a > span",
        "label":"ratingCount"
      }
    ],
    "id": "b4d98de0-ffff-11e8-a4c9-9b9ee9089058",
    "url": "https://www.imdb.com/title/tt0111161/",
    "interval": 60
  }
]
```

### `POST` subscriptions

#### Description
Create a new subscription to feed the spider.

#### Parameters
- **url** _(required)_  - Target website url
- **targets** _(required)_ - List of css selectors from which text contents are expected to be extracted
- **interval** _(required)_ - The interval (in minutes) between scrape

#### Request
```bash
curl -X POST /api/subscriptions -d '{"url":"https://www.imdb.com/title/tt0111161/","targets":"[{\"label\":\"ratingCount\",\"selector\":\"#title-overview-widget > div.vital > div.title_block > div > div.ratings_wrapper > div.imdbRating > a > span\"}]","interval":"60"}' -H "Content-Type: application/json"
```

#### Response
```json
{
  "id": "ef417d30-ffff-11e8-a4c9-9b9ee9089058",
  "url": "https://www.imdb.com/title/tt0111161/",
  "targets": [
    {
      "label":"ratingCount",
      "selector":"#title-overview-widget > div.vital > div.title_block > div > div.ratings_wrapper > div.imdbRating > a > span"
    }
  ],
  "interval": 60,
  "createdAt": 1544833533059,
  "updatedAt": 1544833533059
}
```

### `DELETE` subscriptions

#### Description
Delete a subscription.

#### Parameters
- **id** _(required)_ - Subscription id

#### Request
```bash
curl -X DELETE /api/subscriptions/:id
```

#### Response
```json
{
  "id": "d72c05d0-ffff-11e8-a4c9-9b9ee9089058"
}
```

## Functions List

### `scrape`

#### Description
Scrape target websites and extract target contents.

#### Invoke
```bash
yarn invoke:local scrape -d '{"createdAt":1544833435070,"updatedAt":1544833435070,"targets":[{"selector":"#title-overview-widget > div.vital > div.title_block > div > div.ratings_wrapper > div.imdbRating > a > span","label":"ratingCount"}],"id":"b4d98de0-ffff-11e8-a4c9-9b9ee9089058","url":"https://www.imdb.com/title/tt0111161/","interval":60}'
```

#### Response
```json
[
  {
    "label": "ratingCount",
    "content": "2,025,796"
  }
]
```

### `cron`

#### Description
Fetch subscriptions from database and filter out the ones need to be executed.

#### Invoke
```bash
yarn invoke:local cron
```

#### Response
None

## Development

```bash
# install dependencies
yarn install

# start api server on port 8090
yarn start

# invoke function locally
yarn invoke:local function_name

# invoke remote function
yarn invoke cron function_name
```

## Deploy

```bash
# first setup your aws credentials https://docs.aws.amazon.com/sdk-for-java/v1/developer-guide/setup-credentials.html
yarn deploy
```
