# do-paginate
create an array of numbers which can be used to navigate any dataset or database.

this package is extremely lightweight, fast and easy to use, while also being able to handle any edge-case.

![gif demonstrating example with express](https://github.com/kaspardohrin/do-paginate/blob/master/resources/pagination.gif?raw=true)
*result of running the code example below*

## installation
```bash
  $ npm install do-paginate # or

  $ yarn add do-paginate
```


## technologies
* node: 14.16.0
* typescript: 4.2.3
* npm: 6.14.11
* yarn: 1.22.10


## properties
| name | type | description |
|---|---|---|
| `current_index` | `number` | **required**. the page number that should appear in the middle of the pagination-sequence.<br><br>*example*: when your index is 10 and your offset is 5 a range from 5-15 will be returned. |
| `items_per_page` | `number` | **required**. the amount of items you're displaying on each page. |
| `items_total` | `number` | **required**. the length of your data. This value will determine how many pages can be generated.  |
| `offset` | `number` | **required**. limit the amount of page-numbers being generated by adding the offset parameter. the sequence will be: (offset + index + offset) in length.<br><br>*example*: when your index is 10 and your offset is 5 a range from 5-15 will be returned. |


## code-examples
import the pagination -file and pass in your parameters

basic javascript example
```javascript
  // javascript
  const paginate = require('do-paginate')

  const index = 5
  const items_per_page = 25
  const items_total = 5000
  const offset = 5

  const sequence =
    paginate(index, items_per_page, items_total, offset) // [ 1,2,3,4,5,6,7,8,9,10,11 ]
```

basic typescript example
```typescript
  // typescript
  import paginate from 'do-paginate'

  const index: number = 5
  const items_per_page: number = 25
  const items_total: number = 5000
  const offset: number = 5

  const sequence: Array<number> =
    paginate(index, items_per_page, items_total, offset) // [ 1,2,3,4,5,6,7,8,9,10,11 ]
```

typescript with express and basic routing example
```typescript
  // typescript example with express route
  import express from 'express'
  import paginate from 'do-paginate'
  import cors from 'cors'

  const app = express()

  app.use(cors({ origin: '*' }))

  // request url: /
  app.get('/', async (_, res) =>
    res.send(`<a href='/index=1&limit=25'>test pagination</a>`)
  )

  // request url: /index=1&limit=25
  app.get('/index=:index?&limit=:limit?', async (req, res) => {
    const d_index: number = 1 // default index - used if, for example, none is passed in
    const d_limit: number = 25 // default limit - used if, for example, none is passed in
    const offset: number = 5 // pages to generate

    // get index from url, else fallback to d_index
    const index: number =
      +(req.params?.['index'] as string)?.replace(/[^\d-]/gm, '') || d_index
    // get limit from url, else fallback to d_limit
    const limit: number =
      +(req.params?.['limit'] as string)?.replace(/[^\d-]/gm, '') || d_limit

    const items_total: number = 500 // TODO: get total items

    const pages: Array<number> = paginate(index, limit, items_total, offset)

    // TODO: query database based on: index, limit, and add items to response

    res.send(
      [
        `<style>
        #pagination {
          display: flex;
          justify-content: center;
        }
        a {
          all: unset;
        }
        a:active {
          color: black;
          opacity: 0.5;
        }
        .page {
          text-align: center;
          min-width: 27px;
          padding: 5px 1px;
          cursor: pointer;
        }
        .current {
          font-weight: bold;
        }
        </style>`,

        `<div id="pagination">`,
        // subtract true or false casted to number (true => 1, false => 0)
        `<a class="page" href="/index=${index - (+(index > 1))}&limit=${limit}"> < </a>`,
        // create <a> element with correct href for each number in pages
        pages
          .map(
            (x: number, _: number) =>
              `<a class="page ${(x === index) ? "current" : ""}" href="/index=${x}&limit=${limit}">${x}</a>`
          )
          .join('\n'),
        // add true or false casted to number (true => 1, false => 0)
        `<a class="page" href="/index=${index + (+(index < pages?.[pages.length - 1]))}&limit=${limit}"> > </a>`,
        `</div>`,
      ].join('\n')
    )
  })

  app.listen(process.env.PORT || 8000, () => {
    console.info(`Server started on port ${process.env.PORT || 8000}`)
  })
```


## the uniqueness of this pagination-method
  - the sequence will never contain numbers that could lead to pages being generated with no content to display on them - given the parameters passed in are correct. for example, lets say the index is based on a parameter in an url, and the user changes the index value to 5000000, but you have a total of 5000 items to display with each page containing 25 items, then the sequence generated will end at 200, because at index 201 you'd have an empty page
  - takes a couple ms to generate


## license
MIT


## change-log
`v1.1.6`: update intro: wording and gif, add basic styling to code example<br>
`v1.1.5`: handle not all tests passing more gracefully, add test.js to npm<br>
`v1.1.4`: update readme<br>
`v1.1.3`: update test output, log wasnt showing expected and received values correctly, change some wording<br>
`v1.1.2`: update readme<br>
`v1.1.1`: keep test files in git repo only, changed my mind<br>
`v1.1.0`: change css in code example from width to min-width, refactor pagination algorithm slightly which resulted in ~10% speed increase, add test script, extend build script in package.json<br>
`v1.0.8`: extended code example with arrows to increment index by +1 or -1<br>
`v1.0.7`: forgot to build typescript after making changes, parameter: `offset` is now actually required<br>
`v1.0.6`: update package.json<br>
`v1.0.5`: update readme, and fix typo's<br>
`v1.0.4`: update readme, make parameter: `offset` required<br>
`v1.0.3`: update readme, and fix typo's<br>
`v1.0.2`: update readme, and fix typo's<br>
`v1.0.1`: update readme, and fix typo's<br>
`v1.0.0`: initial publish<br>
