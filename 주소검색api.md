
한글 주소 keyword 검색, 
영문 주소 keyword 검색, 

==> list 출력 

클릭시 위도, 경도 표출 



figma : https://www.figma.com/file/WG8zNUmRM9O8tedqu3QtHq/G1-PCS-Monitoring?node-id=2783%3A20577



### 1. 주소로 위도경도 얻어오기
 #####  1-1. Google Maps Platform  - Geocoding
- 관련링크: https://developers.google.com/maps/documentation/geocoding/intro
- API 형식 : https://maps.googleapis.com/maps/api/geocode/json?address="ADDRESS"&key="YOUR_API_KEY" 
 * YOUR_API_KEY : spike를 위해 임시 생성한 kelee google 계정 key(AIzaSyDyxr6gysi6nhvDFShWRWhqeqpSyTEqS10), 실제 프로젝트에 사용시 key 생성필요
- API 예제: https://maps.googleapis.com/maps/api/geocode/json?address=경기도안양시동안구엘에스로116번길40&key=AIzaSyDyxr6gysi6nhvDFShWRWhqeqpSyTEqS10
- API 예제 리턴 형식
```
{
  "results": [
    {
      "access_points": [
        
      ],
      "address_components": [
        {
          "long_name": "116번길 40",
          "short_name": "116번길 40",
          "types": [
            "premise"
          ]
        },
        {
          "long_name": "LS-ro",
          "short_name": "LS-ro",
          "types": [
            "political",
            "sublocality",
            "sublocality_level_4"
          ]
        },
        {
          "long_name": "Hogye 1(il)-dong",
          "short_name": "Hogye 1(il)-dong",
          "types": [
            "political",
            "sublocality",
            "sublocality_level_2"
          ]
        },
        {
          "long_name": "Dongan-gu",
          "short_name": "Dongan-gu",
          "types": [
            "political",
            "sublocality",
            "sublocality_level_1"
          ]
        },
        {
          "long_name": "Anyang",
          "short_name": "Anyang",
          "types": [
            "locality",
            "political"
          ]
        },
        {
          "long_name": "Gyeonggi-do",
          "short_name": "Gyeonggi-do",
          "types": [
            "administrative_area_level_1",
            "political"
          ]
        },
        {
          "long_name": "South Korea",
          "short_name": "KR",
          "types": [
            "country",
            "political"
          ]
        },
        {
          "long_name": "431-081",
          "short_name": "431-081",
          "types": [
            "postal_code"
          ]
        }
      ],
      "formatted_address": "116번길 40 LS-ro, Hogye 1(il)-dong, Dongan-gu, Anyang, Gyeonggi-do, South Korea",
      "geometry": {
        "location": {
           "lat": 37.3734052,       >>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>    위도
           "lng": 126.9503421       >>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>    경도
        },
        "location_type": "ROOFTOP",
        "viewport": {
          "northeast": {
            "lat": 37.3747541802915,
            "lng": 126.9516910802915
          },
          "southwest": {
            "lat": 37.3720562197085,
            "lng": 126.9489931197085
          }
        }
      },
      "place_id": "ChIJY7eCyVdnezURtaqVtjGbwUA",
      "plus_code": {
        "compound_code": "9XF2+94 Anyang, 경기도 South Korea",
        "global_code": "8Q989XF2+94"
      },
      "types": [
        "establishment",
        "point_of_interest"
      ]
    }
  ],
  "status": "OK"
}
```

### 2. 주소 자동완성 keyword 검색
 #####  2-1. 행정안전부 개발자센터 주소검색솔루션 
- 국내 주소 (국문/영문)
- 관련링크: https://www.juso.go.kr/addrlink/jusoSearchSolutionIntroduce.do

##### 2-2. Google Maps Platform  - Place autocomplete 
- 국/내외 주소 
- 관련링크: https://developers.google.com/places/web-service/autocomplete?hl=ko
- API 형식: https://maps.googleapis.com/maps/api/place/autocomplete/json?input="KEYWORD"&types=geocode&language=kr&key="YOUR_API_KEY"
 * YOUR_API_KEY : spike를 위해 임시 생성한 kelee google 계정 key(AIzaSyDyxr6gysi6nhvDFShWRWhqeqpSyTEqS10), 실제 프로젝트에 사용시 key 생성필요
- API 예제: https://maps.googleapis.com/maps/api/place/autocomplete/json?input=엘에스로&types=geocode&language=kr&key=AIzaSyDyxr6gysi6nhvDFShWRWhqeqpSyTEqS10
- API 예제 리턴 형식 - 잘안됨, 중간중간 빠지는 주소가 있음
```
{
   "predictions" : [
      {
         "description" : "South Korea, Gyeonggi-do, Anyang, Dongan-gu, Hogye-dong, 엘에스로",
         "id" : "549754d5ac9e812203d6223e79d75964544c5867",
         "matched_substrings" : [
            {
               "length" : 4,
               "offset" : 57
            }
         ],
         "place_id" : "ChIJOfk8_FhnezUR8CcEquB33z0",
         "reference" : "ChIJOfk8_FhnezUR8CcEquB33z0",
         "structured_formatting" : {
            "main_text" : "Dongan-gu, Hogye-dong, 엘에스로",
            "main_text_matched_substrings" : [
               {
                  "length" : 4,
                  "offset" : 23
               }
            ],
            "secondary_text" : "South Korea, Gyeonggi-do, Anyang"
         },
         "terms" : [
            {
               "offset" : 57,
               "value" : "엘에스로"
            },
            {
               "offset" : 45,
               "value" : "Hogye-dong"
            },
            {
               "offset" : 34,
               "value" : "Dongan-gu"
            },
            {
               "offset" : 26,
               "value" : "Anyang"
            },
            {
               "offset" : 13,
               "value" : "Gyeonggi-do"
            },
            {
               "offset" : 0,
               "value" : "South Korea"
            }
         ],
         "types" : [ "sublocality_level_4", "sublocality", "political", "geocode" ]
      },
      {
         "description" : "South Korea, Gyeonggi-do, Gunpo-si, 엘에스로",
         "id" : "a7c822e4514b973be10e8155aa8532224ebd6306",
         "matched_substrings" : [
            {
               "length" : 4,
               "offset" : 36
            }
         ],
         "place_id" : "ChIJHTCfn1lnezURgbA29ekS3Jc",
         "reference" : "ChIJHTCfn1lnezURgbA29ekS3Jc",
         "structured_formatting" : {
            "main_text" : "엘에스로",
            "main_text_matched_substrings" : [
               {
                  "length" : 4,
                  "offset" : 0
               }
            ],
            "secondary_text" : "South Korea, Gyeonggi-do, Gunpo-si"
         },
         "terms" : [
            {
               "offset" : 36,
               "value" : "엘에스로"
            },
            {
               "offset" : 26,
               "value" : "Gunpo-si"
            },
            {
               "offset" : 13,
               "value" : "Gyeonggi-do"
            },
            {
               "offset" : 0,
               "value" : "South Korea"
            }
         ],
         "types" : [ "sublocality_level_4", "sublocality", "political", "geocode" ]
      },
      {
         "description" : "South Korea, Gyeonggi-do, Anyang, Dongan-gu, Hogye-dong, 엘에스로91번길",
         "id" : "692e8d4bf302319808a7227ccc4d01156aef7f2b",
         "matched_substrings" : [
            {
               "length" : 4,
               "offset" : 57
            }
         ],
         "place_id" : "ChIJI0_2Z1lnezURvFQYuTmmM0k",
         "reference" : "ChIJI0_2Z1lnezURvFQYuTmmM0k",
         "structured_formatting" : {
            "main_text" : "Dongan-gu, Hogye-dong, 엘에스로91번길",
            "main_text_matched_substrings" : [
               {
                  "length" : 4,
                  "offset" : 23
               }
            ],
            "secondary_text" : "South Korea, Gyeonggi-do, Anyang"
         },
         "terms" : [
            {
               "offset" : 57,
               "value" : "엘에스로91번길"
            },
            {
               "offset" : 45,
               "value" : "Hogye-dong"
            },
            {
               "offset" : 34,
               "value" : "Dongan-gu"
            },
            {
               "offset" : 26,
               "value" : "Anyang"
            },
            {
               "offset" : 13,
               "value" : "Gyeonggi-do"
            },
            {
               "offset" : 0,
               "value" : "South Korea"
            }
         ],
         "types" : [ "sublocality_level_4", "sublocality", "political", "geocode" ]
      },

... (이하 생략) ...

```
