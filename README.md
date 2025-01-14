# GoLive
## 💻 Reactive HTML Server Side Rendered by GoLang over WebSockets 🚀
Use Go and ***Zero JavaScript*** to program reactive front-ends!

![](examples/slider/slider.gif)

## How?
1. Render Server Side HTML
2. Connect to same server using Websocket
3. Send user events
4. Change state of [component](component.go) in server
5. Render Component and get [diff](diff.go)
6. Update instructions are sent to the browser

## Getting Started
- [Extended Version Todo Example](https://github.com/SamHennessy/golive-example)
- [Project Examples](https://github.com/patrickcurl/gowired/tree/master/examples)
- [GoBook - Interactive Go REPL in browser](https://github.com/brendonmatos/gobook)

**Any suggestions are absolutely welcome**

This project it's strongly inspired by Elixir Phoenix LiveView.

## Component Example
```go
package components

import (
	"github.com/patrickcurl/gowired"
	"time"
)

type Clock struct {
	golive.LiveComponentWrapper
	ActualTime string
}

func NewClock() *golive.LiveComponent {
	return golive.NewLiveComponent("Clock", &Clock{})
}

func (t *Clock) Mounted(_ *golive.LiveComponent) {
	go func() {
		for {
			t.ActualTime = time.Now().Format(time.RFC3339Nano)
			time.Sleep((time.Second * 1) / 60)
			t.Commit()
		}
	}()
}

func (t *Clock) TemplateHandler(_ *golive.LiveComponent) string {
	return `
		<div>
			<span>Time: {{ .ActualTime }}</span>
		</div>
	`
}
```

### Server Example
```go

package main

import (
	"github.com/patrickcurl/gowired"
	"github.com/patrickcurl/gowired/examples/components"
	"github.com/gofiber/fiber/v2"
	"github.com/gofiber/websocket/v2"
)

func main() {
	app := fiber.New()
	liveServer := golive.NewServer()

	app.Get("/", liveServer.CreateHTMLHandler(components.NewClock, golive.PageContent{
		Lang:  "us",
		Title: "Hello world",
	}))

	app.Get("/ws", websocket.New(liveServer.HandleWSRequest))

	_ = app.Listen(":3000")
}
```

### That's it!
![](examples/clock/demo.gif)

## More Examples

### Slider
![](examples/slider/slider.gif)

### Simple todo
![](examples/todo/todo.gif)

### All at once using components!
![](examples/all_at_once/all_at_once.gif)

### GoBook
![](examples/gobook.gif)

[Go to repo](https://github.com/brendonmatos/gobook)



