# External Services Example SFDC Consumer

## Description
Hello there, this is the SF side of a 2-part buildout showing a Swagger/OAS service being consumed by Salesforce External Services within a Flow. Find the Heroku component here - https://github.com/cowie/Heroku-External_Services_SF_Row_Segmentation

The hell does that mean? Sometimes you've got more customers than you'd prefer to store in Salesforce. It can be tricky partitioning who's in Salesforce vs who isn't, and you may want to access all customers without storing them live in Salesforce SObjects. This can help with that.

At a high level, we want the Salesforce user to take in a unique identifier (in this case, email or phone), and based on that, first search the local SF Contact table, then a remote table that for this example is located in Heroku Postgres. If the record is not local, but is found remotely, that service will auto-insert the record to Salesforce, and then return the newly minted Salesforce ID. This allows an agent to seamlessly service a contact who may never have existed within the Contact table as of a few seconds ago.

This Repo is focused on the Salesforce side of the coin. It comes with a Named Credential record, External Service record, and two flows. One is a example screen flow to show usage of the core headless subflow, and the other...

...is kinda the main course here. This flow will take in either an Email or a Phone number variable, and then do a quick select query on the Contact object. If it doesn't find one, it will then do the appropes call to one of two fairly identical services on the Heroku side to look in a PG table, find the record, and if found, the Heroku service will also insert. Yeah this is a bad pattern but it was a lazy build.

## Dev, Build and Test
For my sanity, I'm going to assume you wanna work from a scratch org, and use SFDX. If not, replace the scratch stuff with authing to a dev org and convert the source to a metadata package. Thx.

* Create yourself a scratch org. I included an easy basic file for it at `config/rowSegmentOrgDef.json`, so basically `sfdx force:org:create -s -a iWasLazy -f config/rowSegmentOrgDef.json` if ya lazy.
* Run `sfdx force:user:password:generate` and jot down ya username and password.
* Run `sfdx force:org:open` and reset your security token. You will need this.
* Click [![Deploy](https://heroku.com/deploy?template=https://github.com/cowie/Heroku-External_Services_SF_Row_Segmentation)] to create the Heroku master - Environment variables will ask for the type SF environment (production / sandbox) as well as username and password. Make sure to append your security token to your password. Remember how I told you to do that?
* `sfdx force:source:push`
* In Salesforce, change named credential endpoint to your herokuapp's url. Should be somethingsomething.herokuapp.com.
* Activate the two flows if you want to use them in app. Example Screen Flow isn't necessary, but it does show use of the headless subflow to do this work.
* Search the repo, some test data that's in the default data inserted to PG:
  * Email: 
    * dhauck@example.com, gmazzotta@example.com, pfeager@example.com, asong@example.com
  * Phone:
    * (446) 555-9042, (519) 555-7883, (453) 555-6139, (631) 555-8821

## Issues
I find it weird that when I don't want grass, it grows like a weed, but when I do, nothing doing. Like make up your mind.