# Shorten Links For SFMC Mobile Connect

Sometimes we need to shorten a link that is generated dynamically with AMPscript. As you may know we cannot parse JSON in AMPscript, but _bit.ly_ API proposes a XML parameter (`format=xml`). This is what we will use.

## HOW TO
### Logic
1. Creating the personalized URL with AMPscript
2. Building the bit.ly HTTPGET URL
3. Parsing the XML to output the URL



### Commented example

The example below is getting attributes from a Journey Data extension using the SMS activity

```java
%%[
/* getting personalization form the entry data extension */
set @contactId = Contact:Id 
set @firstName = Contact:FirstName
set @lastName = Contact:LastName
set @fullName = ProperCase(concat(@firstName, ' ',@lastName))
/* Prepare the URL that will need to be shorten  */
set @urlPart1 = "https://duckduckgo.com?subkey=" 
set @urlPart2 = concat(@urlPart1,@contactId,'&name=',@fullName) /* Adding the personalized parameters */
set @bitlyBaseApi = "https://api-ssl.bitly.com/v3/shorten?login=BITLY_LOGIN&apiKey=BITLY_API_KeY&format=xml&longUrl=" /* bitly api */
set @urlToShorten = concat(@bitlyBaseApi,@urlPart2) /*concates bitly url with the personalized URL to be shortene*/
set @bitlyShorten = HTTPGet(@urlToShorten) /* calling bitly api */
/* setting the row from the returned XML with a FOR loop */
set @getShortenUrlXml = BuildRowsetFromXml(@bitlyShorten,'//url', 1) 
for @i = 1 to Rowcount(@getShortenUrlXml) do
    Set @row = Row(@getShortenUrlXml, @i)
    Set @finalShortenedUrl = Field(@row,1)
]%%SMS MSG: Cupcake ipsum dolor sit amet sweet sweet muffin. Tootsie roll jelly bear claw.
%%=v(@finalShortenedUrl)=%%
%%[
next @i
]%%

```




## RESSOURCES
- [Check that your XPath expression is working](https://codebeautify.org/Xpath-Tester)
- [AMPscript BuildRowSetFromXML function]( https://developer.salesforce.com/docs/atlas.en-us.noversion.mc-programmatic-content.meta/mc-programmatic-content/buildrowsetfromxml.htm)
- [Bit.ly shorten API](https://dev.bitly.com/links.html#v3_shorten) 
