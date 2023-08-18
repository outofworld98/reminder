client에 아래 명령어로 react-csv 라이브러리를 설치 합니다.

**npm install --save react-csv-downloader**
**npm install --save @types/react-csv**

다음 예제와 같이 사용합니다.

```gherkin
import { CSVLink, CSVDownload } from "react-csv";
 
const csvData = [
  ["firstname", "lastname", "email"],
  ["Ahmed", "Tomi", "ah@smthing.co.com"],
  ["Raed", "Labes", "rl@smthing.co.com"],
  ["Yezzi", "Min l3b", "ymin@cocococo.com"]
]
<CSVLink data={csvData}>Download me</CSVLink>;
// or
<CSVDownload data={csvData} target="_blank" />
```

# 관련 링크)  https://www.npmjs.com/package/react-csv
