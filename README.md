## Project creation process:

<br />

```
typeorm init --database postgres --express
```

gives a template backend with user table configured.

<br />

```
npm i --save-dev nodemon
```

for development

<br />

```
nodemon -w \*.ts -w .env src/index.ts
```

looks for changes in file with extension ts & env and run index.ts

<br />
<br />

## install packages:

```
npm -i package
```

<br />

## install it as dev-dependency:

```
npm -i --save-dev package
```

<br />

## Javascript:

<br />

`[]` syntax allows you to access an object property dynamically using a variable or an expression.

`res.send()` in Express.js, objects and arrays are typically automatically converted to JSON format.

<br />

## Packages:

<br />

`bodyParser.json()` parses the JSON data in the request body and makes it available in req.body.

`morgan package` is for logging info for backend. by default, no backend logs are present and we have chosen tiny as the format.

`Dotenv` is a zero-dependency module that loads environment variables from a .env file into process.env, for this you need to call its config method, this can be found in config.ts in src

`reflect-metadata` allows you to do runtime reflection on types. see `emitDecoratorMetadata` and `experimentalDecorators` in tsconfig.json

<br />

## Access-Control-Allow-Credentials

<br />

When a web page in one domain wants to make a cross-origin request to a different domain and include credentials (such as cookies, HTTP authentication, or client-side certificates), the target server needs to specify that it allows these credentials to be sent with the request.

also, the frontend should make request with `credentials: "include"` which includes credentials with every request, regardless of the origin.

<br />

## Same-origin policy and CSRF

</br>

[full explanation](https://developer.mozilla.org/en-US/docs/Web/Security/Same-origin_policy)

[CSRF](https://www.youtube.com/watch?v=KaEj_qZgiKY&list=PLhixgUqwRTjx2BmNF5-GddyqZcizwLLGP&index=5)

[credentials from client perspective](https://stackoverflow.com/questions/27406994/http-requests-withcredentials-what-is-this-and-why-using-it)

The `same-origin` policy is a critical security mechanism that restricts how a document or script loaded by one origin can interact with a resource from another origin.

Cross-origin writes are typically allowed. Examples are links, redirects, and form submissions. Some HTTP requests require preflight.

if there is Cross-origin reads, the response is not displayed but the code still runs.

## Preflight Request

</br>

Certain CORS requests are considered 'complex' and require an initial OPTIONS request (called the "pre-flight request"). An example of a 'complex' CORS request is one that uses an HTTP verb other than GET/HEAD/POST (such as DELETE) or that uses custom headers.

</br>

```
app.options('/products/:id', cors()) // enable pre-flight request for DELETE request
```

or

```
app.options('*', cors()) // enable pre-flight request for ALL request
```

</br>

## Authorization Header

</br>

The "Authorization" header is part of the HTTP/1.1 specification (defined in RFC 7235) and is widely adopted for including authentication credentials in HTTP requests.

<br />

## Body Parser

<br />

bodyParser.json is responsible for parsing JSON data. It will parse incoming request bodies that have Content-Type: application/json.

</br>

```
POST /test HTTP/1.1
Host: foo.example
Content-Type: application/json
Content-Length: 27

{"key": "value", "another_key": "another_value"}
```

the request doesn't have body field.

there are other methods in bodyparser is parse other type of data.

```
PUT /path/to/resource HTTP/1.1
Host: example.com
Content-Type: application/x-www-form-urlencoded
Content-Length: <length_of_request_body>

key=value&another_key=another_value
```

<br />

## route specific middleware

</br>

`func1` executes first and it call `func2` with `next()`. The order of the functions in the parameters are important as they show the order of execution.

Also, the middlewares should always have `next()` at end to go to next middleware function or send a response to fullfill the request, if not then the request never completes and it gets stuck at the function.

if none of the middleware functions in the stack sends a response or calls `next()` without an argument, the server will `not get stuck`. Express.js will automatically send an empty response (with a status code of 200 OK) to the client to indicate that the request has been successfully handled.

</br>

```
STUCK:

func1(req, res, next){
    console.log('dsds')
    next()
}

func2(req, res, next){
    console.log('sadasdasd')
}

app.get('/', func1, func2)



VALID:

func1(req, res, next){
    console.log('dsds')
    next()
}

func2(req, res, next){
    console.log('sadasdasd')
    next() // or res.send('dss')
}

app.get('/', func1, func2)
```

</br>

## global middleware

</br>

```
app.use(func1)
```

</br>

## external middleware function

use passport.authenticate as example, when we run this function it gives a custom middleware function to handle passport authenticate, which you can call with the passed req, res and next params

```
    (req, res, next) => {

        const state = JSON.stringify(req.query.type)

        passport.authenticate('google', {
          scope: ['email', 'profile'],
          session: false,
          state, // Pass the state as a parameter
        })(req, res, next)
    }
```

## express session

</br>

Express Session primarily stores a session ID in the browser as cookie and the session data associated with that ID in a server-side store.

Here's how it works:

1. When a user visits a website, Express Session generates a unique session ID for that user's session. This session ID is typically stored in a cookie in the user's browser.

2. The session ID is used to look up session data on the server-side. This session data is where you can store properties and values related to the user's session.

the id in session property of `req.session.id` and updates to this will also be stored in server side store.

</br>

```
app.use((req, res, next) =>{
    if(req.session.viewCount){
        ++req.session.viewCount
    }else{
        req.session.viewCount
    }
})
```

</br>

## symmetric cryptography

In symmetric cryptography, the same key is used for both the encryption and decryption of data. This means that both the sender and the receiver of the encrypted data share the same secret key.

it has the challenge of securely sharing and managing the secret keys, especially in large-scale systems.

The encrypted data is transmitted over a potentially insecure channel (e.g., the internet).

</br>

## passport

</br>

[reddit post](https://www.reddit.com/r/node/comments/rk3o1g/can_anyone_help_me_understand_how_to_use/)

[better doc](https://github.com/jwalton/passport-api-docs)

[medium post 1](https://shivamvv.medium.com/google-oauth2-along-with-jwt-using-node-js-and-passport-e7d119e81678)

[medium post 2](https://medium.com/nerd-for-tech/google-oauth2-0-authentication-using-node-js-and-passportjs-1a77f42b1111)

[multiple strategies](https://rrawat.com/blog/multi-provider-oauth2-nodejs)

The verify callback of a Passport strategy is typically called for every request that uses that specific strategy

</br>

## passport jws startegy

</br>

Passport itself does not handle the sending and receiving of JWTs. Instead, Passport focuses on the authentication and authorization aspects of your application.

Also, the JWT is not encrypted so anybody with the jwt decode it and view the data.

The signature ensures that the JWT's contents have not been tampered with during transit or storage. If anyone modifies the header or payload, the signature will no longer match, and the JWT will be invalid.

The "Bearer" prefix is used in the context of HTTP authentication to indicate that the token being sent in the "Authorization" header is a bearer token. A bearer token is a type of access token that is presented by the client to access protected resources on a server. The "Bearer" prefix helps distinguish bearer tokens from other types of credentials that might be used for authentication.

</br>

### Sending JWT

</br>

1. When a user logs in or a token needs to be issued, you typically generate a JWT on your server.

2. Once the JWT is generated, you need to send it to the client, typically as part of a response. Passport is not directly involved in this step; it's up to your application's logic to send the JWT as an HTTP response, often in a JSON object or an HTTP header (commonly the Authorization header).

</br>

### Receiving JWT

</br>

1. When a client makes a request to a protected route, it should include the JWT for authentication, usually in the Authorization header as a Bearer token.

2. Passport is responsible for extracting the JWT from the incoming request, verifying its authenticity, and using it to authenticate and authorize the user.

```
app.get('/protected', passport.authenticate('jwt', { session: false }), (req, res) => {

  res.json({ message: 'Access granted!', user: req.user });

});
```

</br>

## SSL and HTTPS

</br>

One of the most common uses of SSL/TLS is in securing web traffic. When SSL/TLS is used with HTTP, it becomes HTTPS (HTTP Secure). This is the protocol used to secure data exchange between your web browser and a web server, ensuring that sensitive information like login credentials or credit card numbers is transmitted securely.

SSL/TLS relies on digital certificates issued by trusted Certificate Authorities. These certificates contain information about the server's identity and public key. Browsers and clients use these certificates to verify the server's authenticity.

SSL/TLS also provides data integrity checks to ensure that the data sent from one end is received without any modifications at the other end. This prevents data tampering during transmission.

TLS has succeeded SSL

</br>

## storing jwt securely

</br>

[How to securely store JWT tokens](https://dev.to/gkoniaris/how-to-securely-store-jwt-tokens-51cf)

when you use cookies for jwt, then set it to `httpOnly` which will prevent js from accessing it.

cookies also automatically get sent to server at every request.

When a cookie has the `secure` flag set, it means that the cookie should only be transmitted over secure, encrypted connections, typically HTTPS.

set `sameSite` to lax to block third party cookie from being sent by cross origin, but to make this work the frontend and backend need to have sam domain

</br>

## error middleware

</br>

you can call the next function with an error parameter to pass control to the next middleware or route handler while indicating that an error has occurred during processing.

```
next(err)

function handleError(err, _req, res, _next) {
  res.status(err.statusCode || 500).send(err.message)
}

```

</br>

## custom fields in request object

</br>

```
func1(req, res, next){
    req.customId = 1
    next()
}

func1(req, res, next){
    console.log(req.customId)
}
```

</br>

## Static Controller method and dbContext

</br>

bad idea, period.

[Why would I use static methods for database access](https://stackoverflow.com/questions/21413726/why-would-i-use-static-methods-for-database-access#:~:text=In%20the%20particular%20case%20of,instead%20coding%20to%20an%20implementation)

</br>

```
In the particular case of a data access layer I'd avoid static methods for one simple reason... coupling.

No alternate implementations, no test stubs, no dependency inversion at all. The business logic has a dependency arrow pointing to an infrastructure concern (the data access layer), whereas that should be the other way around.
```

</br>

## nodemon:

<br />

```
nodemon -w *.ts -w .env src/index.ts
```

\*.ts part is a glob pattern that matches all .ts files in the current directory and its subdirectories.

<br />

## AWS

### S3 Bucket

in the main page search for `S3` and create a bucket with `default settings` but choose a `region` where the site will have high traffic.

Search `IAM` to create a new user, give them `AmazonS3FullAccess` and after creating the user, you'll be redirected to user management page `(Access Management > Users)`, click the user you created and go to security credentials to create the `AWS_ACCESS_KEY_ID` and `AWS_SECRET_ACCESS_KEY` which your node.js environment should have access to.

#### simple solution for testing:

put the command below in console and run the app

```
export AWS_ACCESS_KEY_ID=AKIATIGGTFVKBPARWAZA
export AWS_SECRET_ACCESS_KEY=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
```

<br />

### Implementation Strategy 1 (slow)

send the image to the backend, backend send it to aws and stores the file url in db, then we can send the url to the frontend, the frontend will use this to get the file

#### making a bucket public

[more about public access](https://stackoverflow.com/questions/70473682/aws-s3-bucket-what-is-the-difference-between-block-public-access-and-a-blank)

```
So, if you want to make an bucket or objects within publicly accessible, first you need to disable this additional layer of security (disabling it does not means the bucket or objects are public, only means than you can make them public) and then make them public via ACL
```

after turning off `Block all public access` in `Permissions`, you need to add `ACL` to activate the public get access

Add the following in `bucket policy` in permission:

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "PublicReadGetObject",
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::BUCKET_NAME/*"
        }
    ]
}
```

this will give read access to ur file to anyone with URL

<br />

### Implementation Strategy 2

set the `CORS` in `permissions` tab of the bucket

```
[
    {
        "AllowedHeaders": [
            "*"
        ],
        "AllowedMethods": [
            "PUT",
            "POST"
        ],
        "AllowedOrigins": [
            "*"
        ],
        "ExposeHeaders": []
    }
]
```

then create a `signed_url` in backend and send it to frontend so that it can store the file to AWS itself.

signed_url are short-lived.

### Links:

[signed url 1](https://zaccharles.medium.com/s3-uploads-proxies-vs-presigned-urls-vs-presigned-posts-9661e2b37932)

[signed url 2](https://stackoverflow.com/questions/70612710/nodejs-aws-s3-createpresignedpost-returns-error-cannot-read-properties-of-undefi)

## seeding:

<br />

seeding is a concept of populating the db with some data at the start of the server.

`package: typeorm-extensions`

look at commit message that has the word "seeding" in it.

<br />

## Snake Naming Strategy

<br />

TypeORM will automatically generate and map table and column names using the "snake_case" convention.

For example, if you have an entity named UserDetails with a property firstName, the corresponding table in the database will be named user_details, and the column will be named first_name.

`package: typeorm-naming-strategy`

<br />

## Storing date time in postgreSQL

<br />

by default the TIMESTAMP WITH TIME ZONE type (our datetime type) is stored as UTC in postgreSQL, this means that even if your db timezone is set as AST, ADT, the stored time will not be stored as AST unless we change the default format. instead it'll be stored UTC PLUS the offset to reflect that timezone. So, if there is +00 offset, then it means the time zone is UTC.

```
SET TIME ZONE 'AST';

SELECT '2023-07-11 15:43:00+00'::timestamptz;

>> '2023-07-11 11:43:00-04'
```

https://www.postgresql.org/docs/current/datatype-datetime.html

<br />

## How to insert DateTime for table

<br />

```
INSERT INTO table(last_updated) VALUES(TIMESTAMP WITH TIME ZONE 'Tuesday July 11, 2023 at 08:00 AST');

id |    last_updated
----+-------------------------+
13 |   2023-07-11 11:00:00+00

```

we can store the timestamp we get from environment canada as it is without worrying about conversions. However, we might need conversion when we retrieve data from db.

```
select last_updated at time zone 'ADT' from table;
```

which returns the date time in atlantic date and time
