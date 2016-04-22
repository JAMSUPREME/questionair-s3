# questionair-s3
A concept for making a questionnaire that stores data in RDS and deploys itself to S3. Probably handle routing on the front-end with angular

# Front end

* Angular (for simplicity and routing)
* Workflow (similar to stuff I did in questionair but for JS) - could probably be built relatively easily with promises to avoid nesting hell.

# Back end

* *Probably* AWS lambda using JS and maybe API gateway? We just want a few easy endpoints like... 
  * `POST eligibility/users` - Creates a user "session" of sorts that we will connect answers to
  * `PUT eligibility/users/{uuid}/answers` - Updates the collection of answers. It'll be easiest to just have this PUT and no POST, as we'll probably always pass the whole answers collection. Makes updates and popping easy.
  * `GET eligibility/users/{uuid}/answers` - Gets all the answers provided until now
  * `POST eligibility/users/{uuid}/last-answer` - The last answer provided. Doesn't belong in `/answers` because the user is currently "on" that question, but we need it for pre-population. *Note:* It's probably possible for us to refactor the workflow in some way so that this is no longer necessary. Either by 1) Not using a "replay" mechanism and having a fancier collection or 2) Cleverly do an off-by-one replay and know to pop it when the user answers again.
  * `GET eligibility/users/{uuid}/last-answer` - The last answer to be popped
  * I'm OK with front-end generating a UUID or hash since we assume it has a short TTL and clashing is unlikely

# Infra

* S3 for storage (with web site hosting)
* RDS for storing questionnaire information (Redis would be easiest, plus easy TTL)
* Ansible playbook to spin things up (maybe also a vagrant file so it can be spun up locally)
* GitLab for CI? (just for fun)

# Cool things this gives us

* S3 and Lambda are *basically* infinitely scalable, which means we don't have to pay for scaling or worry about it.
* Serverless (or almost serverless), so less server mgmt.
* Very "microservice" inclined. Small services in Lambda that can be tested and updated/versioned standalone.
* Strong separation of concerns between front end and back end (we could swap out our API with whatever). Plus we can test the front end with normal Angular stuff
