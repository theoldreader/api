# The Old Reader API

## TODO

- Starring items
- Tagging items
- Social features
- OAuth

## Apps

### iOS

- [Feeddler](https://itunes.apple.com/us/app/feeddler-rss-reader-for-ipad/id364873582?mt=8)
- [Feeddler Pro](https://itunes.apple.com/us/app/feeddler-rss-reader-pro/id365710282?mt=8)

### Android

- [gReader](https://play.google.com/store/apps/details?id=com.noinnion.android.greader.reader)
- [gReader Pro](https://play.google.com/store/apps/details?id=com.noinnion.android.greader.readerpro)
- [D7 Reader](https://play.google.com/store/apps/details?id=com.droidlab7.reader)
- [D7 Reader Pro](https://play.google.com/store/apps/details?id=com.droidlab7.reader.pro)
- [The Old Reader (unofficial, beta)](https://play.google.com/store/apps/details?id=com.toreaderapp)
- [Listener Launcher](https://play.google.com/store/apps/details?id=com.piaci.listener.app)
- [Wizz Widget](https://play.google.com/store/apps/details?id=com.snowbee.colorize.hd)

### Web

- [inboxN.com](http://www.inboxn.com/)
- [Oxnt (send to Kindle)](http://oxnt.me/)


### Other

- [ThOR](http://store.ovi.com/content/382129) (Symbian)
- [Liferea](http://lzone.de/liferea/) (Linux)
- [FeedSpider](https://developer.palm.com/appredirect/?packageid=com.othelloventures.feedspider) (WebOS)
- [tornexto (go to next item in a folder)](https://github.com/cowens/tornexto) (Bookmarklet)

## Libraries

- [PHP](https://github.com/zelenin/Tor) (by [zelenin](https://github.com/zelenin))

## Contributing

You are welcome to improve documentation, please feel free to send us pull requests with your adjustments.

If you find any bugs or feel that something is not working as expected, please create a github issue or contact us at [api@theoldreader.com](mailto:api@theoldreader.com). 

## Current API

Current API tries to mimic the unofficial Google Reader API.

Please note that even though you can use API both via http and https, we highly encourage you to use https for security reasons. 

### Differences

- Currently only json format is supported
- We use BSON ObjectId for user, item, subscription identifiers (example: ```00157a17b192950b65be3791```)
- We also use ObjectId in Stream Ids instead of URL (example: ```feed/00157a17b192950b65be3791```)
- All user ids are replaced by ```-``` in state/label strings (examples: ```user/-/state/com.google/reading-list```, ```user/-/label/Folder```)

### Authentication

All requests you will be sending to the API should be authenticated by a token.

Please note that The Old Reader allows users to sign in via OAuth (Google or Facebook), but the only way to get an API token for a now is to set password in settings and use it for authentication.

#### Getting a token

Sample request:

```	
curl -d "client=YourAppName&accountType=HOSTED_OR_GOOGLE&service=reader&Email=test@krasnoukhov.com&Passwd=..." https://theoldreader.com/accounts/ClientLogin
```

Sample response:

```
SID=none
LSID=none
Auth=LyTEJPvTJiSPrCxLu46d
```

Please note that you can also get a token by requesting following url: ```/reader/api/0/accounts/ClientLogin```

Also you might want to add ```output=json``` to POST parameters for corresponding result.

#### Using a token

Include `Authorization: GoogleLogin auth=TOKEN` HTTP header in all your requests. For example:

	Authorization: GoogleLogin auth=LyTEJPvTJiSPrCxLu46d

### Errors

All errors have following format:

XML:

```
<?xml version="1.0" encoding="UTF-8"?>
<errors type="array">
  <error>XML output currently is not supported</error>
</errors>
```

JSON:

```
{
	errors: [
		"Temporary unavailable, please try later"
	]
}
```

### Status

After a couple unsuccessful requests you might try to obtain current The Old Reader status:

GET [https://theoldreader.com/reader/api/0/status?output=json](https://theoldreader.com/reader/api/0/status?output=json)

Everything is fine:

```
> curl https://theoldreader.com/reader/api/0/status?output=json
{"status":"up"}
```

Something is wrong:

```
> curl https://theoldreader.com/reader/api/0/status?output=json
{"status":"down","description":"Database issues","redirect":"http://status.theoldreader.com"}
```

### Generic methods

#### Token

GET [https://theoldreader.com/reader/api/0/token](https://theoldreader.com/reader/api/0/token)

```
> curl https://theoldreader.com/reader/api/0/token -H "Authorization: GoogleLogin auth=LyTEJPvTJiSPrCxLu46d"
LyTEJPvTJiSPrCxLu46d
```

Added for compatibility.

#### User info

GET [https://theoldreader.com/reader/api/0/user-info?output=json](https://theoldreader.com/reader/api/0/user-info?output=json)

```
> curl https://theoldreader.com/reader/api/0/user-info?output=json -H "Authorization: GoogleLogin auth=LyTEJPvTJiSPrCxLu46d"
{"userId":"00157a17b192950b65be3791","userName":"Test","userProfileId":"00157a17b192950b65be3791","userEmail":"test@krasnoukhov.com","isBloggerUser":false,"signupTimeSec":1370709105,"isMultiLoginEnabled":false}
```

#### Preferences list

GET [https://theoldreader.com/reader/api/0/preference/list?output=json](https://theoldreader.com/reader/api/0/preference/list?output=json)

Added for compatibility.

#### Friend list

GET [https://theoldreader.com/reader/api/0/friend/list?output=json](https://theoldreader.com/reader/api/0/friend/list?output=json)

Added for compatibility for now. Will be used for The Old Reader social features.

### Folders

#### Tag list

GET [https://theoldreader.com/reader/api/0/tag/list?output=json](https://theoldreader.com/reader/api/0/tag/list?output=json)

Contains folder list.

#### Stream preferences list

GET [https://theoldreader.com/reader/api/0/preference/stream/list?output=json](https://theoldreader.com/reader/api/0/preference/stream/list?output=json)

Contains information about subscriptions ordering for root (folders and subscriptions ordering) and user folders (subscriptions ordering).

#### Update stream preferences

POST [https://theoldreader.com/reader/api/0/preference/stream/set](https://theoldreader.com/reader/api/0/preference/stream/set)

Added for compatibility.

#### Renaming folder

POST [https://theoldreader.com/reader/api/0/rename-tag](https://theoldreader.com/reader/api/0/rename-tag)

Parameters:

	s=user/-/label/Folder
	dest=user/-/label/NewFolder

#### Removing folder

POST [https://theoldreader.com/reader/api/0/disable-tag](https://theoldreader.com/reader/api/0/disable-tag)

Removes folder and moves subscriptions to default folder.

Parameters:

	s=user/-/label/Folder

### Subscriptions

#### Unread count

GET [https://theoldreader.com/reader/api/0/unread-count?output=json](https://theoldreader.com/reader/api/0/unread-count?output=json)

Contains unread counts for all items (reading list), each folder and subscription.

#### Subscriptions list

GET [https://theoldreader.com/reader/api/0/subscription/list?output=json](https://theoldreader.com/reader/api/0/subscription/list?output=json)

Each subscription contains ```url```, ```htmlUrl``` and ```iconUrl```.

#### Subscriptions OPML

GET [https://theoldreader.com/reader/subscriptions/export](https://theoldreader.com/reader/subscriptions/export)

#### Adding subscription

POST [https://theoldreader.com/reader/api/0/subscription/quickadd?quickadd=blog.theoldreader.com](https://theoldreader.com/reader/api/0/subscription/quickadd?quickadd=blog.theoldreader.com)

#### Updating subscription

POST [https://theoldreader.com/reader/api/0/subscription/edit](https://theoldreader.com/reader/api/0/subscription/edit)

Parameters:

	ac=edit
	
	# Stream Id
	s=feed/00157a17b192950b65be3791
	
	# Change subscription title
	t=Title
	
	# Move subscription to folder with a given title
	a=user/-/label/Folder
	
	# Move subscription to default folder (no difference what r is)
	r=user/-/label/Folder

#### Removing subscription

POST [https://theoldreader.com/reader/api/0/subscription/edit](https://theoldreader.com/reader/api/0/subscription/edit)

Parameters:

	ac=unsubscribe
	s=feed/00157a17b192950b65be3791 # Stream Id

### Items

#### Item ids

GET [https://theoldreader.com/reader/api/0/stream/items/ids?output=json](https://theoldreader.com/reader/api/0/stream/items/ids?output=json)

Parameters:

Filter by stream:

	# All items
	s=user/-/state/com.google/reading-list
	
	# Read items
	s=user/-/state/com.google/read
	
	# Folder
	s=user/-/label/...
	
	# Subscription
	s=feed/...

Exclude items:
	
	# Only unread
	xt=user/-/state/com.google/read

Other parameters:

	n=1000 # Limit items
	r=o # Reverse sorting
	c=… # Continuation
	nt=1371645508 # Older than (applied only when sorting is normal)
	ot=1371645508 # Newer than (applied only when sorting is reverse)

#### Item contents

POST [https://theoldreader.com/reader/api/0/stream/items/contents?output=json](https://theoldreader.com/reader/api/0/stream/items/contents?output=json)

Parameters:

	# Item ids
	i=…&i=…&i=…
	
	# Atom output
	output=atom

#### Stream contents

GET [https://theoldreader.com/reader/api/0/stream/contents?output=json](https://theoldreader.com/reader/api/0/stream/contents?output=json)

Parameters:
	
	# Same as for ids
	
	# Atom output
	output=atom

Also there is an atom feed for specific Stream Id:

GET [https://theoldreader.com/reader/atom/feed/00157a17b192950b65be3791](https://theoldreader.com/reader/atom/feed/00157a17b192950b65be3791) 

And state:

GET [https://theoldreader.com/reader/atom/user/-/state/com.google/read](https://theoldreader.com/reader/atom/user/-/state/com.google/read) 

And folder:

GET [https://theoldreader.com/reader/atom/user/-/label/Folder](https://theoldreader.com/reader/atom/user/-/label/Folder) 

#### Marking all as read

POST [https://theoldreader.com/reader/api/0/mark-all-as-read](https://theoldreader.com/reader/api/0/mark-all-as-read)

Parameters:

	# All items
	s=user/-/state/com.google/reading-list
	
	# Folder
	s=user/-/label/...
	
	# Subscription
	s=feed/...
	
	# Older than timestamp in nanoseconds
	ts=1371645508000000

#### Updating items

POST [https://theoldreader.com/reader/api/0/edit-tag](https://theoldreader.com/reader/api/0/edit-tag)

Parameters:

	# Item ids
	i=…&i=…&i=…
	
	# Mark as read
	a=user/-/state/com.google/read
	
	# Mark as unread
	r=user/-/state/com.google/read
