# json resume: Schema

[Original URL](http://jsonresume.org/schema/)

> What is it? JSON Resume is a community driven open source initiative to create JSON-based standard for resumes. Why JSON? We believe that the strengths of the JSON format makes it a good fit for...

## What is it?

JSON Resume is a community driven open source initiative to create JSON-based standard for resumes.

## Why JSON?

We believe that the strengths of the JSON format makes it a good fit for resumes. It's lightweight, easy to use and it's perfect to build tools for!

We also feel that the [JSON Schema](http://json-schema.org) is mature enough for writing usable semantics.

## Open Source

The schema is [open source](https://github.com/jsonresume/resume-schema) and community-driven. We release everything we do under the MIT license.

```
{

 "basics": {
 "name": "John Doe",
 "label": "Programmer",
 "picture": "",
 "email": "[email protected]",
 "phone": "(912) 555-4321",
 "website": "http://johndoe.com",
 "summary": "A summary of John Doe...",
 "location": {
 "address": "2712 Broadway St",
 "postalCode": "CA 94115",
 "city": "San Francisco",
 "countryCode": "US",
 "region": "California"
 },
 "profiles": [{
 "network": "Twitter",
 "username": "john",
 "url": "http://twitter.com/john"
 }]
 },
 "work": [{
 "company": "Company",
 "position": "President",
 "website": "http://company.com",
 "startDate": "2013-01-01",
 "endDate": "2014-01-01",
 "summary": "Description...",
 "highlights": [
 "Started the company"
 ]
 }],
 "volunteer": [{
 "organization": "Organization",
 "position": "Volunteer",
 "website": "http://organization.com/",
 "startDate": "2012-01-01",
 "endDate": "2013-01-01",
 "summary": "Description...",
 "highlights": [
 "Awarded 'Volunteer of the Month'"
 ]
 }],
 "education": [{
 "institution": "University",
 "area": "Software Development",
 "studyType": "Bachelor",
 "startDate": "2011-01-01",
 "endDate": "2013-01-01",
 "gpa": "4.0",
 "courses": [
 "DB1101 - Basic SQL"
 ]
 }],
 "awards": [{
 "title": "Award",
 "date": "2014-11-01",
 "awarder": "Company",
 "summary": "There is no spoon."
 }],
 "publications": [{
 "name": "Publication",
 "publisher": "Company",
 "releaseDate": "2014-10-01",
 "website": "http://publication.com",
 "summary": "Description..."
 }],
 "skills": [{
 "name": "Web Development",
 "level": "Master",
 "keywords": [
 "HTML",
 "CSS",
 "Javascript"
 ]
 }],
 "languages": [{
 "language": "English",
 "fluency": "Native speaker"
 }],
 "interests": [{
 "name": "Wildlife",
 "keywords": [
 "Ferrets",
 "Unicorns"
 ]
 }],
 "references": [{
 "name": "Jane Doe",
 "reference": "Reference..."
 }]

}
```
