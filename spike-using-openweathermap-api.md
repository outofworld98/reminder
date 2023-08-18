https://openweathermap.org/ 
api 사용

api Key : 사이트에서 생성된 키 사용

onecall api:
https://api.openweathermap.org/data/2.5/onecall?lat={lat}&lon={lon}&exclude={part}&appid={API key}

위 api로 받는 정보:
Current weather
Minute forecast for 1 hour
Hourly forecast for 48 hours
Daily forecast for 7 days
Government weather alerts
Historical weather data for the previous 5 days

call 방법:
SecurityJavaConfig의

default-src 에 'unsafe-inline' 'unsafe-eval' https://api.mapbox.com https://api.openweathermap.org/data/2.5/onecall
script-src 에  'unsafe-inline'
connect-src 에 'self' https://api.mapbox.com https://api.openweathermap.org/data/2.5/onecall https://events.mapbox.com ;"
worker-src 에 'self' blob: https:  ;"

설정 추가

ex)
```gherkin
.contentSecurityPolicy("default-src 'self' blob: *.mapbox.com 'unsafe-inline' 'unsafe-eval' https://api.mapbox.com https://api.openweathermap.org/data/2.5/onecall; "
                        + "img-src 'self' data: blob: *.mapbox.com; "
                        + "font-src data: *.mapbox.com; "
                        + "script-src 'self' 'unsafe-inline' 'unsafe-eval' blob: *.mapbox.com; "
                        + "connect-src 'self' https://api.mapbox.com https://api.openweathermap.org/data/2.5/onecall https://events.mapbox.com ;"
                        + "worker-src 'self' blob: https:  ;"
                        + "style-src 'unsafe-inline' *.mapbox.com;"
                )
```


사용 예제
```gherkin
    async getWeather(){
        let lat = "36.6504095"
        let lon  = "127.446075"

        const weather : Weather = await this.props.weatherRepository.getWeatherData(lat, lon)

        console.log("weather: "+ JSON.stringify(weather))
    }
```
