### Requirements
- Google OAuth -> Link with Google calendar
- Every user will have a unique link to their scheduler page which can be visited to schedule, no user will be shown without that link
- Users can send a request to schedule a meeting based on availability
	- description & title of the meeting
- users can accept the request from their cal dashboard
- these accepted requests should be [[Google Calendar Updation using Node.js|saved on google calendar]] of both sender and receiver along with a G-Meet link
- only show the time which can be blocked (don't reflect the timespan which is already blocked by events)
- **Note:** Send emails to users when they receive a schedule request or when their request is accepted. (Microservice using AWS Lambda)
	- Two Approaches:
	1. Flush email data in SQS and attach it to a Lambda which processes those 
	2. Directly call a AWS lambda to trigger those emails -> Preferred as of now

**NOTE:** Make sure no user email is shown until the booking is confirmed.

---
### Tech Stack
- Backend -> node.js (typescript)
- DB -> PostgreSQL (Prisma ORM)
- Frontend -> React.js