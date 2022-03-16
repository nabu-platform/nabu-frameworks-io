@title This is the title

# The process

If you want to start processing before all the data is in: allow segmentation?
Basically allow for a "limit" parameter on the provider, it should only get as many entries as requested. If we have fewer, we don't rerun, if we have exactly as many as requested, we rerun (_AFTER_ we have run the finalization), if we have more than requested we assume it is not properly supported

IN:

- run transfer service
- store all the details, either as FINAL (if no transformers required) or NON-FINAL (transformation required)
- run finalization service (probably to delete the source data)
- run any transformers that exists
- store all the new details as FINAL
- start task(s) where relevant
 

OUT:

- store all the details, either as FINAL (no transformation required) or NON-final (transformation required)
- run transformers
- store new details as FINAL
- run transfer service
- store return results
- run finalization service


# In

## Poll

If an IN channel has a channel provider, you can poll it with the given properties. You can either run the poll from your own service or you can use a scheduler (e.g. task framework) to run the nabu.frameworks.io.services.poll service with the correct input parameters.

If a server fails while the initial poll is still going on, it will _not_ restart the poll on reboot. Instead it will wait for the next polling interval to trigger.

If a server fails _after_ initial pickup of the data but _before_ transforming it must rerun finalization

## Push

If you have an IN channel, you can also push data to it, at that point the IN channel does not require a channel provider. For instance you might set up a dynamic endpoint for data pushing which does not require a channel provider nor properties.
Alternatively you might receive data via another mechanism which can not easily be polled and should only be pushed. Or it can be combined with polling.

# Out

## Push

You can push data to an OUT channel. If that channel has a configured channel provider, the data will be delivered to the intended endpoint.
If the channel does not have a channel provider, it is more of a dropbox, you can get a historical overview of the data delivered there and do something else with it.






---------
save the context in the database
	> or service context?
	> need to reconstitute it for datastore storage!
	> maybe allow explicit passing of context?

result handler in database is nice for recover
	> especially for historic stuff (e.g. a file from last week with a directory with a current day parameter will not be picked up again)
	> not only recover but replay as well

- early processing (if many files, start processing before all files are known)
- 	> can submit tasks as they are ready? e.g. single file processing
- 	> may want all files at once? one task with batch input
- variables (e.g. date formatting in the properties etc)
- 	> can do with preprocessing (just wrap a flow around it)
- 	> trickier if you wanna manage entirely from GUI

- retry (if transient error)
- 	> already in task framework
- recover (in case of system failure, automatically retry/finish pending stuff)
- 	> already in task framework (though no difference between commit/done)
- batching of for example a directory poll
- 	> already in task framework
- reuse of expensive state (e.g. ftp connection)
- 	> the "directory wrapper" service can simply wrap around the file provider, hence reusing the connection?
- 		> not sure, no resource access...
- 		

can already schedule stuff from tasks
	> can simply schedule a provider service? (e.g. send email) with the necessary properties?
	> dynamic properties still an issue...
	> can allow glue service to resolve content? e.g. start the string content with "=" and it can be evaluated by glue?


missing:
- keep extensive logs of retry etc?
- bug in properties apparently? no list support etc
	> need object to properties level support
- post & pre process?
	zipping & unzipping?




is a post processor just another ioProvider? (post processing is on IN)
we pass it "requests" -> the data to post process
and we expect "responses" -> the data to actually process

pre processing is on OUT
-> you preprocess the data into a separate transfer and then store that to actually call the resulting channel