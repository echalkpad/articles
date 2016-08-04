
[Original URL](https://ukiahsmith.com/blog/go-marshal-and-unmarshal-json-with-time-and-url-data/?utm_source=golangweekly)

> Go has a robust JSON library for consuming and outputting JSON. The standard json.Marshal() and json.Unmarshal() functions do a great job of aligning primitive types and converting them from Go into...

Go has a robust JSON library for consuming and outputting JSON. The standard `json.Marshal()` and `json.Unmarshal()` functions do a great job of aligning primitive types and converting them from Go into JSON, and vise versa. Beyond the basic types you will have to create custom marshaling methods on your types and write some logic to handle the conversion process. We will go over writing these custom methods for types that contain time and URL data.

The example we will work with is a Link data type, a struct that contains information about a web URL. It contains time.Time and url.URL data types, and a string and int for the title and ID.

```
type Link struct {
    ID int `json:"id,string,omitempty"`
    Title string `json:"title,string"`
    URL url.URL `json:"url,string"`
    CreatedAt time.Time `json:"createdat,string"`
}
```

This gives us four unique types to work use, with url.URL and time.Time being the interesting ones.

JSON itself does not have complex data types for for time or URLs, they are stored as strings. In order to use them as data in Go we need be specific in how we get the strings and transform them into data.

We will use another building block of Go, the [interface](https://golang.org/doc/effective_go.html#interfaces_and_types), to enhance our Link struct and allow for custom JSON handeling. The [json package](https://golang.org/pkg/encoding/json/) defines two interfaces, Marshaler and Unmarshaler. Let us look at Unmarshaler first.

## Unmarshaling

In order for our Link type to implement the Unmarshaler interface we need to satisfy the interface method; which is `UnmarshalJSON()`. This method will be called when we use the `json.Unmarshal()` method instead of the standard unmarshal methods in the json package.

```
type Unmarshaler interface {
 UnmarshalJSON([]byte) error
}
```

The `UnmarshalJSON()` method takes a slice of bytes, processes it, stores the data in the type, and returns and error type. We will return nil if there is no error.

In our example here we are marshaling everything into a map of strings, with strings as the keys. Then we are iterating over the keys and manually Unmarshal the data into our struct depending on the type.

```
func (l *Link) UnmarshalJSON(j []byte) error {
    var rawStrings map[string]string

    err := json.Unmarshal(j, &rawStrings)
    if err != nil {
        return err
    }

    for k, v := range rawStrings {
        if strings.ToLower(k) == "id" {
            l.ID, err = strconv.Atoi(v)
            if err != nil {
                return err
            }
        }
        if strings.ToLower(k) == "title" {
            l.Title = v
        }
        if strings.ToLower(k) == "url" {
            u, err := url.Parse(v)
            if err != nil {
                return err
            }
            l.URL = *u
        }
        if strings.ToLower(k) == "createdat" {
            t, err := time.Parse(time.RFC3339, v)
            if err != nil {
                return err
            }
            l.CreatedAt = t
        }
    }

    return nil
}
```

Parsing Time is done in the same way, except we need to match the time format in the parse function with the JSON data supplied.

The time value in the JSON could be in a variety of formats. In this example we are expecting that to be RFC3339\. You will need to know the format of the time as it is used in the source JSON, and use Go's [time format](https://golang.org/pkg/time/#pkg-constants) to specify the layout so it can be correctly mapped to a time.Time object.

## Marshaling

Marshaling our Link struct into JSON is easier than the previous example. Simply we add a `MarshalJSON()` method which satisfies the `json.Marshaler` interface. Inside of which we mimic the basic structure of our Link struct and convert everything to a primitive type that JSON supports. For time and URL we use the standard library functions for each to return a string.

```
func (l Link) MarshalJSON() ([]byte, error) {
    basicLink := struct {
        ID int `json:"id"`
        Title string `json:"title"`
        URL string `json:"url"`
        CreatedAt string `json:"createdat"`
    }{
        ID: l.ID,
        Title: l.Title,
        URL: l.URL.String(),
        CreatedAt: l.CreatedAt.Format(time.RFC3339),
    }

    return json.Marshal(basicLink)
}
```

Marsheling our example is more direct than Unmarshaling, but still gives us the control we need to override the layout used by `json.Marshal()` and format time as we require it. This will also make things easier for you when working with large complex data structures that need to be output as JSON.

## Go and JSON

JSON inherited some of the wonkyness from Javascript, but with a few lines of code we were able to wrangle the primitive types into something useful in our Go application. The Marshaler and Unmarshaler interfaces make it easy to hook your custom code into the standard JSON marshaling process. The examples here are a simple version of what you will need to do for your own data types.

### Like what you read? Share it:

[__ Facebook](https://facebook.com/sharer.php?u=https://ukiahsmith.com/blog/go-marshal-and-unmarshal-json-with-time-and-url-data) [__ Twitter](https://twitter.com/intent/tweet?url=https://ukiahsmith.com/blog/go-marshal-and-unmarshal-json-with-time-and-url-data&text=Go%3a%20Marshal%20and%20Unmarshal%20JSON%20with%20time%20and%20URL%20data&via=Supermighty) [__ Google+](https://plus.google.com/share?url=https://ukiahsmith.com/blog/go-marshal-and-unmarshal-json-with-time-and-url-data)
