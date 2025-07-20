#  Building an API with Go
<table>
  <tr>
  </h1></td>
    <td>
      <p align="right">
        <!-- Add icons for the main technologies used in this project. Find more at: https://github.com/devicons/devicon -->
        <img src="https://raw.githubusercontent.com/devicons/devicon/master/icons/go/go-original-wordmark.svg" alt="go" width="60" height="60"/> 
        <img src="https://raw.githubusercontent.com/devicons/devicon/master/icons/linux/linux-original.svg" alt="linux" width="60" height="60"/> 
        <img src="https://raw.githubusercontent.com/devicons/devicon/master/icons/docker/docker-original-wordmark.svg" alt="docker" width="60" height="60"/>
        <!-- Add a database icon if applicable, e.g., postgresql, mongodb -->
      </p>
    </td>
  </tr>
</table>

## Description
<br/> 
<!-- 
  Provide a high-level summary of your project here. 
  - What is the purpose of this API? (e.g., "In this project, we build and deploy a RESTful API using Go...")
  - What problem does it solve?
  - What are the key technologies involved?
-->
<br />
<br/> Project Architecture: <br/>
<!-- Add a link to your project's architecture diagram here. You can create one and upload it to your repository. -->
<img src="[LINK_TO_YOUR_ARCHITECTURE_DIAGRAM_HERE]"/>
<br/> 
<!-- 
  Provide a more detailed explanation of the project's architecture.
  - Explain the flow of data.
  - Describe the main components (e.g., API server, database, middleware).
  - Mention the deployment environment (e.g., "The API is deployed on a RHEL 9 server and managed using systemd...").
-->
<br/>

## Tech Stack & Services

| **Service / Technology** | **Purpose**                                                                 |
|--------------------------|-----------------------------------------------------------------------------|
| **Go (Golang)**          | The core language used to build the high-performance REST API.              |
| **[API Framework]**      | <!-- e.g., Gin, Gorilla Mux, net/http --> Used to handle routing and HTTP requests. |
| **[Database]**           | <!-- e.g., PostgreSQL, MongoDB, SQLite --> Provides persistent storage for application data. |
| **Docker (Optional)**    | Used to containerize the application for consistent deployment.             |
| **Systemd**              | Manages the API as a background service on the Linux server.                |
| **[Other Tools]**        | <!-- e.g., JWT for auth, Viper for config --> Describe any other libraries or tools. |

### **Notes for Usage**
1. **Required Software**: Go, Git, [Database Name].
2. **Deployment Target**: A Linux server (e.g., RHEL, Ubuntu) with `systemd`.

<p align="center">
  
### **Prerequisites**  
- Have an [AWS account] or access to a Linux server.
- Install [Go (Golang)].
- Install the [AWS CLI] (if deploying to AWS).

</p>

## Step 1: Local directory path set up

<br/> The first and most important step is to initialize the project as a Go module. To do this, I will run the go mod init command followed by the repository URL I plan to use. <br/>

```bash
go mod init github.com/DevinLiggins14/go-rest-api
```

<img src"<img width="638" height="122" alt="image" src=https://github.com/user-attachments/assets/a9b7f75e-8d95-460a-985e-503a7e880705/>

<br/> 
I start my project by running `go mod init` to create the essential `go.mod` file. This command officially establishes my project as a self-contained Go module. It gives my project a unique name that I will use for managing packages. This file will track all the external libraries and their exact versions that I add later. Following this practice ensures my project's dependencies are clear and that it can be reliably built by anyone.
<br/>
```bash
go mod init github.com repo url
```

</p>

## Step 2: Defining the API's data contract and entrypoint
<br/>Next create a new directory api and a file inside it named api.go. The goal here is to define the "shape" of the data that the API will accept in requests and send back in responses. Placing this in its own package means there is a clear reusable data contract that other parts of the application can rely on.<br/>
```go
// Filename: api/api.go

package api

import (
	"encoding/json"
	"net/http"
)

// CoinBalanceParams defines the structure for the request parameters.
// I use a struct here because it's Go's primary way to model data,
// and it maps perfectly to JSON payloads.
type CoinBalanceParams struct {
	Username string
}

// CoinBalanceResponse defines the structure for the API's response.
// This ensures that all responses from this endpoint have a consistent format,
// which is crucial for the clients that will use my API.
type CoinBalanceResponse struct {
	// Success Code, e.g., 200 for OK, 400 for Bad Request
	Code int
	// Balance holds the actual data being requested.
	Balance int64
	// Username is included to confirm whose balance is being returned.
	Username string
}
```


<br/> With the project initialized, the next step is to create the main entrypoint for the executable. This is handled by the cmd/api/main.go file. The responsibilities of this file are to configure the application's core components, such as logging and routing and to launch the web server.

This file follows the standard Go convention of placing the main package for an executable inside a cmd subdirectory. <br/>

```go
// Filename: cmd/api/main.go

package main

import (
	"fmt"
	"net/http"

	// A powerful, lightweight router for building Go web services.
	"github.com/go-chi/chi"
	// A structured, pluggable logging library.
	log "github.com/sirupsen/logrus"

	// This is the project's internal package where the API logic will reside.
	// The name 'avukadin/goapi' should be replaced with the project's actual module path.
	"github.com/avukadin/goapi/internal/handlers"
)

func main() {
	// Configure the logger to report the calling function's file and line number.
	// This adds valuable context for debugging.
	log.SetReportCaller(true)

	// Initialize a new router from the chi library.
	// The router is responsible for mapping incoming HTTP requests to handler functions.
	var r *chi.Mux = chi.NewRouter()

	// Delegate the responsibility of defining API routes to the handlers package.
	// The router 'r' is passed to the handler, which will attach all the
	// specific endpoints (e.g., /balance, /user).
	handlers.Handler(r)

	// Print a confirmation message and a banner to the console to show the server is starting.
	fmt.Println("Starting GO API service...")
	fmt.Println(`
 ______     ______        ______     ______   __    
/\  ___\   /\  __ \      /\  __ \   /\  == \ /\ \   
\ \ \__ \  \ \ \/\ \     \ \  __ \  \ \  _-/ \ \ \  
 \ \_____\  \ \_____\     \ \_\ \_\  \ \_\    \ \_\ 
  \/_____/   \/_____/      \/_/\/_/   \/_/     \/_/ `)

	// Start the HTTP server, instructing it to listen on port 8000 on the local machine.
	// The server will use the configured chi router 'r' to process all requests.
	err := http.ListenAndServe("localhost:8000", r)
	if err != nil {
		// If the server fails to start (e.g., port is in use), log the error and exit.
		log.Error(err)
	}
}
```

<img src""/>

<br/>  
<br/>


## Step 2: Create an api.go file
<br/><br/>
<img src""/>

<br/>  
<br/>


## Step 2: Create an api.go file
<br/><br/>
<img src""/>

<br/>  
<br/>


## Step 2: Create an api.go file
<br/><br/>
<img src""/>

<br/>  
<br/>


## Step 2: Create an api.go file
<br/><br/>
<img src""/>

<br/>  
<br/>


## Step 2: Create an api.go file
<br/><br/>
<img src""/>

<br/>  
<br/>
