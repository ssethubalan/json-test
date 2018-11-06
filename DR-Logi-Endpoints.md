# Digital River Logitech Endpoints
## Price and Stock availability endpoint

Get stock availability and price from the endpoint below

##### JSONP request
https://buy.logitech.com/store/logib2c/DisplayDRProductInfo/version.2/externalReferenceID.910-005131/externalReferenceID.910-005137/content.stockStatus+price+buyLink+sku/output.json/jsonp.productDetailsmain


## DR (Digital River) auth flow

#### Step 1:
The very first step of the auth flow is to get the sessionToken from the DR store. 
The endpoint for that is below

###### Endpoint:
GET https://buy.logitech.com/store/logib2c/SessionToken?format=json

###### Example
https://buy.logitech.com/store/logib2c/SessionToken?format=json

###### Returns
{ "session_token": "C986EC34ED354871789C30E7385EFB0D" }


#### Step 2:
Get the jwt token from the logitech endpoint for performing cart operations.

###### Endpoint:
POST https://www.logitech.com/bin/cart/create?action=authorize

###### Params:
action - required (action is one of these depending on the endpoint authorize, getcart, updatecart, updatelineitems,deletelineitems)
session_token - required
locale - required (locale is of format language-country example en-us, de-de,etc.,)
Example:
https://www.logitech.com/bin/cart/create?action=authorize&session_token=C986EC34ED354871789C30E7385EFB0D&locale=en-us

###### Returns
{"drtoken":"eyJhbGciOiJIUzI1NiJ9.eHsiYWNjZXNzX3Rva2VuIjoiNjZmMjhhNWRiMWJlYTY5YjVkMTAzOGVlNmQwM2E2NWViMWUxZTg5ZTg0NzQwMDk0ZGNmZjdhMTEwMWI5YWM0MWU2YTVjNjQ0MzBhY2YwNmY5ZTNmZmIyMzNjYWQ1NTU2ODMwZDQ5YmFmZGY1NzhjNWU5YWIwNzE2YzYzNzJlOWI5MmEzMmY5ZWM0ZWNmODgyZTRkOWQzODU0ZDQyM2IwOCIsInRva2VuX3R5cGUiOiJiZWFyZXIiLCJleHBpcmVzX2luIjo4NjMzNiwicmVmcmVzaF90b2tlbiI6IjY2ZjI4YTVkYjFiZWE2OWI1ZDEwMzhlZTZkMDNhNjVlYjFlMWU4OWU4NDc0MDA5NGRjZmY3YTExMDFiOWFjNDFjNTk1MzhmOWM3MGQ3MzA5NThhYTlkMGIyZTBiNGZjYjM1MDQ3YjIwZjc2M2FiNzIxZmFlNjI5Yjg3M2NhMzQ1IiwibG9jYWxlIjoiZW5fVVMifQ.ilhYtk1pUrO2iVU3l8rsF_ziqPiAe08KkxOlUKHZIPc","expiresIn":86336}

Note: At present there are some issues in expiresIn value so don't use this. Instead do the auth on every page.



## CART Operations

All cart operations require the drtoken obtained in Step 2 of DR auth flow.
All params content type are "application/x-www-form-urlencoded"


### 1. Get Cart

#### 	Endpoint
	GET https://www.logitech.com/bin/cart/create?action=getcart

#### Params:
	dr_token - required (not required if sending the token in header)

#### 	header:
	LOGI-DR-TOKEN - dr_token (Note you can either send the authtoken or dr_token in params, don't send both)

#### Returns
	Cart entries / line items present in the cart, this response is called a cart response
	
	
### 2. Update Cart	
	
#### 	Endpoint
	POST https://www.logitech.com/bin/cart/create?action=updatecart
	
#### 	Params:
	sku_id - required (sku id of a product or the externalreferenceid of the product in Digital River store)
	theme_id - required (The themeid of the site associated in Digital River store)
	dr_token - required
	quantity - number , optional (if not specified the quantity defaults to 1)
	
#### 	Returns
	returns the cart response
	
### 4. Update line Items
	
#### 	Endpoint
	POST https://www.logitech.com/bin/cart/create?action=updatelineitems
	
#### 	Params:
	sku_id - required (sku id of a product or the externalreferenceid of the product in Digital River store)
	theme_id - required (The themeid of the site associated in Digital River store)
	dr_token - required
	quantity - number , optional (if not specified the quantity defaults to 1)
	lineitem_id - optional/required (depends on the scenario)
	lineitem_action - optional(add, subtract, update https://developers.digitalriver.com/v1.0/reference#v1shoppersmecartsactivelineitemsbylineitemsidpost)
	custom_attributes - optional/required (depends on the scenario)
	
		
#### 	Returns 
	200 and line item response if lineitem_id is specified
	201 and a dummy response if no lineitem_id is specified
	
###Example
	
#### Without line items
	
##### Endpoint
	https://www.logitech.com/bin/cart/create?action=updatelineitems&sku_id=939-001558&theme_id=4807228000&dr_token=xxxxxxxx&quantity=1
	
This particular endpoint is useful when you want to create a line item in the cart. 
The above endpoint creates a line item for the mentioned sku. returns a dummy response. Need to use getcart call to see the cart entries.

#### 	With line items
	
##### 	Endpoint
	https://www.logitech.com/bin/cart/create?action=updatelineitems&sku_id=939-001558&theme_id=4807228000&dr_token=xxxxxxxxxx&quantity=10&lineitem_id=12200534534&lineitem_action=update

This particular endpoint is useful when you want to update a specific line item or cart entry, 	in this case the lineitem_id is required.lineitem_action is optional if not specified the quatities mentioned in the body will be updated in the cart. This endpoint returns a line item response which is different from cart response.
	
### 5. Delete cart/lineitems

#### 	Endpoint
	POST https://www.logitech.com/bin/cart/create?action=deletelineitems
	
#### Params:
	dr_token - required
	lineitem_id - optional/required (if lineitem_id is given then only that particular line item is deleted, if not all the lineitems in the cart are deleted)
	

### 6. Checkout
		
#### Endpoint
	GET https://www.logitech.com/bin/cart/view
	
#### Params:
	drToken: required
	themeId: required
	
#### Returns
	returns a 302 and redirects to the Blue DR cart page.
	
