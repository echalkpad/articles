
[Original URL](https://medium.com/@matryer/tiny-abstractions-using-functions-in-go-39cd7b9a1db2)

> Sometimes you want to abstract a piece of functionality in your code so that you can swap out implementations, like for test code, or based on some configuration.The traditional way to do this is to...

Sometimes you want to abstract a piece of functionality in your code so that you can swap out implementations, like for test code, or based on some configuration.

The traditional way to do this is to make an interface:

```
type EmailSender interface {
 Send(*mail.Message) error
}
```

Then we could have a SendGrid implementation, an App Engine one, one for test (that just records the activity so we can make assertions against it) and maybe a no-op alternative for when email is disabled via configuration.

That is a lot of new types.

While things remain simple (i.e. one method), we can consider just using a function value instead:

```
sendEmailFunc func(*mail.Message) error
```

Like this:

```
type Server struct {
 // SendEmail is a function that sends an email.
 SendEmail func(*mail.Message) error
}

func NewServer() *Server {
 return &Server{
 SendEmail: func(m *mail.Message) error {
 // default implementation
 },
 }
}

// or even pass functions into other functions:

func CreateNewUser(
 email, password string,
 sendMailFunc func(*mail.Message) error,
) { ... }
```

Now, we haven't added lots of types, but we can change our implementation very easily and even make use of closures when doing so for when we need a little state:

```
func TestSignup(t *testing.T) {

 s := NewServer()

 var emails []*mail.Message
 var emailErr error
 s.SendEmail = func(m *mail.Message) error {
 emails = append(emails, m)
 return emailErr
 }

 // run sign-up code

 if len(emails) != 1 {
 t.Errorf("One email expected to be sent")
 }

 // simulate email error (which will get returned
 // by SendEmail function above)
 emailErr = errors.New("nope")

 // run sign-up code again and test
 // error case

}
```

This pattern is used by the [Dial method of http.Transport](https://golang.org/pkg/net/http/#Transport) in the standard library.
