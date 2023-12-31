# simple-site-monitoring-nodejs

```javascript
const https = require('node:https');

let INTERVAL = 120000
let TESTS = [
  {url: 'https://www.don-tara.ru/', includes: 'Qk0GWKCgTPdop_2_dMUEsqrjY-v2nqTsUiYX-selimU', title: 'Don Tara 200'},
  {url: 'https://www.don-tara.ru/robots.txt', includes: 'Host: https://www.don-tara.ru/', title: 'Don Tara robots.txt'}
];

async function getPage(url) {
  return new Promise((resolve) => {
    let data = ''
    https.get(url, res => {
      res.on('data', chunk => {
        data += chunk
      })
      res.on('end', () => {
        resolve(data);
      })
    })
  })
}

let strings = {}
TESTS.map(({url, includes, title}, index) => {
  let t
  let date = new Date().toISOString().replace(/T/, ' ').replace(/\..+/, '');
  strings[index] = `${title} ${date} \n`
  function run() {
    getPage(url)
      .then(d => d.includes(includes))
      .then(is => {
        date = new Date().toISOString().replace(/T/, ' ').replace(/\..+/, '');
        console.clear()
        if (is) {
          strings[index] = `\x1b[102m ${title} ${date} \x1b[0m \n`
        } else {
          strings[index] = `\x1b[101m ${title} ${date} \x1b[0m \n`
        }
        let accumulate = ''
        for (const stringsKey in strings) {
          accumulate += strings[stringsKey]
        }
        console.log(accumulate)
        t = setTimeout(run, INTERVAL)
      })
    if(!t) t = setTimeout(run, INTERVAL)
  }
  run();
})



```
