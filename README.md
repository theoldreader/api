# The Old Reader API

Current API tries to mimic the unofficial Google Reader API.

### Differences

- Currently only json format is supported
- We use BSON ObjectId for user, item, subscription identifiers (example: ```00157a17b192950b65be3791```)
- We also use ObjectId in Stream Ids instead of URL (example: ```feed/00157a17b192950b65be3791```)
- All user ids are replaced by ```-``` in state/label strings (examples: ```user/-/state/com.google/reading-list```, ```user/-/label/Folder```)

### Authentication

All requests you will be sending to the API should be authenticated by a token.

#### Getting a token

Sample request:

```	
curl -d "accountType=HOSTED_OR_GOOGLE&service=reader&Email=test@krasnoukhov.com&Passwd=..." https://theoldreader.com/accounts/ClientLogin

```

Sample response:

```
SID=none
LSID=none
Auth=LyTEJPvTJiSPrCxLu46d
```

#### Using a token

Include `Authorization: GoogleLogin auth=TOKEN` HTTP header in all your requests. For example:

	Authorization: GoogleLogin auth=LyTEJPvTJiSPrCxLu46d

### Generic methods

#### Token

GET [https://theoldreader.com/reader/api/0/token](https://theoldreader.com/reader/api/0/token)

```
> curl https://theoldreader.com/reader/api/0/token -H "Authorization: GoogleLogin auth=LyTEJPvTJiSPrCxLu46d"
LyTEJPvTJiSPrCxLu46d
```

Added for compatibility.

#### User info

GET [https://theoldreader.com/reader/api/0/user-info](https://theoldreader.com/reader/api/0/user-info)

```
> curl https://theoldreader.com/reader/api/0/user-info -H "Authorization: GoogleLogin auth=LyTEJPvTJiSPrCxLu46d"
{"userId":"00157a17b192950b65be3791","userName":"Test","userProfileId":"00157a17b192950b65be3791","userEmail":"test@krasnoukhov.com","isBloggerUser":false,"signupTimeSec":1370709105,"isMultiLoginEnabled":false}

```

#### Preferences list

GET [https://theoldreader.com/reader/api/0/preference/list](https://theoldreader.com/reader/api/0/preference/list)

Added for compatibility.

#### Friend list

GET [https://theoldreader.com/reader/api/0/friend/list](https://theoldreader.com/reader/api/0/friend/list)

Added for compatibility for now. Will be used for The Old Reader social features.

### Folders

#### Tag list

GET [https://theoldreader.com/reader/api/0/tag/list](https://theoldreader.com/reader/api/0/tag/list)

Contains folder list.

#### Stream preferences list

GET [https://theoldreader.com/reader/api/0/preference/stream/list](https://theoldreader.com/reader/api/0/preference/stream/list)

Contains information about subscriptions ordering for root and user folders.

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

GET [https://theoldreader.com/reader/api/0/unread-count](https://theoldreader.com/reader/api/0/unread-count)

Contains unread counts for all items (reading list), each folder and subscription.

#### Subscriptions list

GET [https://theoldreader.com/reader/api/0/subscription/list](https://theoldreader.com/reader/api/0/subscription/list)

Each subscription contains ```url``` and ```htmlUrl```.

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

GET [https://theoldreader.com/reader/api/0/stream/items/ids](https://theoldreader.com/reader/api/0/stream/items/ids)

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

#### Item contents

POST [https://theoldreader.com/reader/api/0/stream/items/contents](https://theoldreader.com/reader/api/0/stream/items/contents)

Parameters:

	# Item ids
	i=…&i=…&i=…
	
	# Atom output
	output=atom

#### Stream contents

GET [https://theoldreader.com/reader/api/0/stream/contents](https://theoldreader.com/reader/api/0/stream/contents)

Parameters:
	
	# Same as for ids
	
	# Atom output
	output=atom
 

#### Updating items

POST [https://theoldreader.com/reader/api/0/edit-tag](https://theoldreader.com/reader/api/0/edit-tag)

Parameters:

	# Item ids
	i=…&i=…&i=…
	
	# Mark as read
	a=user/-/state/com.google/read
	
	# Mark as unread
	r=user/-/state/com.google/read
