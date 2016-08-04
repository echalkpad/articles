# digitalocean/godo

[Original URL](https://github.com/digitalocean/godo)

> Godo is a Go client library for accessing the DigitalOcean V2 API. You can view the client API docs here: <http://godoc.org/github.com/digitalocean/godo> You can view DigitalOcean API docs here:...

[![Build Status](https://camo.githubusercontent.com/64d762bb5df52eab0702c6ad9c20ee8750e96515/68747470733a2f2f7472617669732d63692e6f72672f6469676974616c6f6365616e2f676f646f2e737667)](https://travis-ci.org/digitalocean/godo)

Godo is a Go client library for accessing the DigitalOcean V2 API.

You can view the client API docs here: <http://godoc.org/github.com/digitalocean/godo>

You can view DigitalOcean API docs here: <https://developers.digitalocean.com/documentation/v2/>

## [](https://github.com/digitalocean/godo#usage)Usage

```
import "github.com/digitalocean/godo"
```

Create a new DigitalOcean client, then use the exposed services to access different parts of the DigitalOcean API.

### [](https://github.com/digitalocean/godo#authentication)Authentication

Currently, Personal Access Token (PAT) is the only method of authenticating with the API. You can manage your tokens at the DigitalOcean Control Panel [Applications Page](https://cloud.digitalocean.com/settings/applications).

You can then use your token to create a new client:

```
import "golang.org/x/oauth2"

pat := "mytoken"
type TokenSource struct {
 AccessToken string
}

func (t *TokenSource) Token() (*oauth2.Token, error) {
 token := &oauth2.Token{
 AccessToken: t.AccessToken,
 }
 return token, nil
}

tokenSource := &TokenSource{
 AccessToken: pat,
}
oauthClient := oauth2.NewClient(oauth2.NoContext, tokenSource)
client := godo.NewClient(oauthClient)
```

## [](https://github.com/digitalocean/godo#examples)Examples

To create a new Droplet:

```
dropletName := "super-cool-droplet"

createRequest := &godo.DropletCreateRequest{
 Name: dropletName,
 Region: "nyc3",
 Size: "512mb",
 Image: godo.DropletCreateImage{
 Slug: "ubuntu-14-04-x64",
 },
}

newDroplet, _, err := client.Droplets.Create(createRequest)

if err != nil {
 fmt.Printf("Something bad happened: %s\n\n", err)
 return err
}
```

### [](https://github.com/digitalocean/godo#pagination)Pagination

If a list of items is paginated by the API, you must request pages individually. For example, to fetch all Droplets:

```
func DropletList(client *godo.Client) ([]godo.Droplet, error) {
 // create a list to hold our droplets
 list := []godo.Droplet{}

 // create options. initially, these will be blank
 opt := &godo.ListOptions{}
 for {
 droplets, resp, err := client.Droplets.List(opt)
 if err != nil {
 return nil, err
 }

 // append the current page's droplets to our list
 for _, d := range droplets {
 list = append(list, d)
 }

 // if we are at the last page, break out the for loop
 if resp.Links == nil || resp.Links.IsLastPage() {
 break
 }

 page, err := resp.Links.CurrentPage()
 if err != nil {
 return nil, err
 }

 // set the page we want for the next request
 opt.Page = page + 1
 }

 return list, nil
}
```

## [](https://github.com/digitalocean/godo#versioning)Versioning

Each version of the client is tagged and the version is updated accordingly.

Since Go does not have a built-in versioning, a package management tool is recommended - a good one that works with git tags is [gopkg.in](http://labix.org/gopkg.in).

To see the list of past versions, run `git tag`.

## [](https://github.com/digitalocean/godo#documentation)Documentation

For a comprehensive list of examples, check out the [API documentation](https://developers.digitalocean.com/documentation/v2/).

For details on all the functionality in this library, see the [GoDoc](http://godoc.org/github.com/digitalocean/godo) documentation.

## [](https://github.com/digitalocean/godo#contributing)Contributing

We love pull requests! Please see the [contribution guidelines](https://github.com/digitalocean/godo/blob/master/CONTRIBUTING.md).
