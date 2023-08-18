**관련 사이트**
react-big-calendar
https://jquense.github.io/react-big-calendar/examples/index.html

css- 선택자
https://www.codingfactory.net/10792

Calendar에 eventPropGetter 통해 className 또는 style 수정
https://stackoverflow.com/questions/34587067/change-color-of-react-big-calendar-events


설치 방법
```gherkin
Client-onm에서 
npm i --save react-big-calendar
npm install --save @types/react-big-calendar
npm install moment --save
npm install style-loader css-loader --save

설치 후

webpack.config에 

            {
                test: /\.css$/,
                loader: 'style-loader!css-loader'
            }

추가

css파일 import 후 사용
```

**example)** 
MyCalendar.tsx

```gherkin
 import React from "react"
import {Calendar, momentLocalizer} from 'react-big-calendar'
import moment from 'moment'
import './MyCalendar.css'


interface event {
    id: string,
    title: string,
    start: Date,
    end: Date,
    allDay?: boolean
    resource?: any,
}

interface Props {

}

interface State {
    showTest: boolean
    eventList: event[]
    eventId: string
    e: any
}

export class MyCalendar extends React.Component<Props, State> {
    constructor(props: Readonly<Props>) {
        super(props)
        this.state = {
            showTest: false,
            eventList: [],
            eventId: "",
            e: {},
            content: ""
        }
    }

    onClickSlot(e: any) {
        this.setState({
            showTest: !this.state.showTest,
            e
        })
    }

    addEvent() {
        let e = this.state.e


        let selectedEventList: event[] = e.slots.map((a: event) => {
            return {
                id: this.state.eventId,
                start: a,
                end: a,
                title: this.state.content,
                allDay:true
            }
        })
        let eventList = this.state.eventList
        selectedEventList.forEach(event => {
            eventList.push(event)
        })

        this.setState({
            eventList
        })
    }

    renderPopUp() {
        return <div>
            <input type="text"
                   value={this.state.eventId}
                   onChange={(e) => {
                       this.setState({
                           eventId: e.target.value
                       })
                   }}
            />
            <input type="text"
                   value={this.state.content}
                   onChange={(e) => {
                       this.setState({
                           content: e.target.value
                       })
                   }}
            />

            <button onClick={this.addEvent.bind(this)}>button</button>
        </div>
    }

    render() {
        const localizer = momentLocalizer(moment)
```


**방법1**
```gherkin
        return <div>
            <Calendar
                localizer={localizer}
                events={this.state.eventList}
                startAccessor="start"
                endAccessor="end"
                style={{height: 500}}
                selectable={true}
                onSelectSlot={(e) => {
                    this.onClickSlot(e)
                }}
                eventPropGetter={
                    (event, start, end, isSelected) => {
                        let className = ""

                        if (event.id == "1") {
                            className = "lightgreen"
                        } else if (event.id == "2") {
                            className = "blue"
                        } else if (event.id == "3") {
                            className = "yellow"
                        } else if (event.id == "4") {
                            className = "white"
                        }

                        return {
                            className: className
                        }
                    }
                }
            />
            {this.state.showTest && this.renderPopUp()}
        </div>
```

MyCalendar.css
```gherkin
div[class~="lightgreen"] {
 border-left: 5px solid lightgreen !important;
}

div[class~="white"] {
    border-left: 5px solid white !important;
}

div[class~="yellow"] {
    border-left: 5px solid yellow !important;
}

div[class~="blue"] {
    border-left: 5px solid blue !important;
}
```


**방법2**
```gherkin
        return <div>
            <Calendar
                localizer={localizer}
                events={this.state.eventList}
                startAccessor="start"
                endAccessor="end"
                style={{height: 500}}
                selectable={true}
                onSelectSlot={(e) => {
                    this.onClickSlot(e)
                }}
                eventPropGetter={
                    (event, start, end, isSelected) => {
                        let newStyle = {
                            backgroundColor: "transparent",
                            color: 'black',
                            borderRadius: "0px",
                            borderLeftColor: "red",
                            borderStyle: "solid",
                            borderLeftWidth: "5px",
                            borderRight: "none",
                            borderTop: "none",
                            borderBottom: "none",
                        };

                        if (event.id == "1") {
                            newStyle.borderLeftColor = "lightgreen"
                        } else if (event.id == "2") {
                            newStyle.borderLeftColor = "blue"
                        } else if (event.id == "3") {
                            newStyle.borderLeftColor = "yellow"
                        } else if (event.id == "4") {
                            newStyle.borderLeftColor = "white"
                        }

                        return {
                            className: "",
                            style: newStyle
                        };
                    }
                }
            />
            {this.state.showTest && this.renderPopUp()}
        </div>
```
    }
}

