# x2js
Automatically exported from code.google.com/p/x2js

#Basic Usage
##XML to JSON
```javascript
// Create x2js instance with default config
var x2js = new X2JS();
var xmlText = "<MyRoot><test>Success</test><test2><item>val1</item><item>val2</item></test2></MyRoot>";
var jsonObj = x2js.xml_str2json( xmlText );
```

##JSON to XML
```javascript
// Create x2js instance with default config
var x2js = new X2JS();
var jsonObj = { 
     MyRoot : {
                test: 'success',
                test2 : { 
                    item : [ 'val1', 'val2' ]
                }
      }
};
var xmlAsStr = x2js.json2xml_str( jsonObj );
```

#Working with Arrays
##Configure XML structure knowledge beforehand
```javascript
var x2js = new X2JS({
        arrayAccessFormPaths : [
           "MyArrays.test.item"
        ]
    });


var xmlText = "<MyArrays>"+
                "<test><item>success</item><item>second</item></test>"+
        "</MyArrays>";
    
        
var jsonObj = x2js.xml_str2json( xmlText );
    console.log(jsonObj.MyArrays.test2.item[0]);
```

##Or use the utility function
```javascript
var x2js = new X2JS();
var xmlText = "<MyArrays>"+
                "<test><item>success</item><item>second</item></test>"+
        "</MyArrays>";

var jsonObj = x2js.xml_str2json( xmlText );
    console.log(x2js.asArray(jsonObj.MyArrays.test.item)[0]);
```

#Working with XML Attributes

##Access to XML attributes
```javascript
// Create x2js instance with default config
var x2js = new X2JS();   

var xmlText = "<MyOperation myAttr='SuccessAttrValue'>MyText</MyOperation>";
var jsonObj = x2js.xml_str2json( xmlText );

// Access to attribute
console.log(jsonObj.MyOperation._myAttr);

// Access to text
console.log(jsonObj.MyOperation.__text);
// Or
console.log(jsonObj.MyOperation.toString());
```

##Configure a custom prefix to attributes
```javascript
var x2js = new X2JS({
    attributePrefix : "$"
});

var xmlText = "<MyOperation myAttr='SuccessAttrValue'>MyText</MyOperation>";

var jsonObj = x2js.xml_str2json( xmlText );

// Access to attribute
console.log(jsonObj.MyOperation.$myAttr);
```

#Working with Namespaces
##Parse XML with namespaces
```javascript
    var xmlText = "<testns:MyOperation xmlns:testns='http://www.example.org'>"+
"<test>Success</test><test2 myAttr='SuccessAttrValueTest2'><item>ddsfg</item><item>dsdgfdgfd</item><item2>testArrSize</item2></test2></testns:MyOperation>";

    var jsonObj = x2js.xml_str2json( xmlText );
    console.log(jsonObj.MyOperation.test);
```
##Create XML with namespaces. Option #1
```javascript
    var testObjC = {
            'm:TestAttrRoot' : {
                '_tns:m' : 'http://www.example.org',
                '_tns:cms' : 'http://www.example.org',
                MyChild : 'my_child_value',
                'cms:MyAnotherChild' : 'vdfd'
            }
    }
```
##Create XML with namespaces. Option #2
```javascript
    // Parse JSON object constructed with another NS-style
    var testObjNew = {
            TestAttrRoot : {
                __prefix : 'm',
                '_tns:m' : 'http://www.example.org',
                '_tns:cms' : 'http://www.example.org',
                MyChild : 'my_child_value',
                MyAnotherChild : {
                        __prefix : 'cms',
                        __text : 'vdfd'
                }
            }
    }
    
    // Parse JSON object with namespaces
    var xmlDocStr = x2js.json2xml_str(
        testObjNew
    );
```

#Working with Date Time
##Working with XML DateTime. Configuring it beforehand
```javascript
    x2js = new X2JS({
        datetimeAccessFormPaths : [
           "MyDts.testds" /* Configure it beforehand */
        ]
    });

    xmlText = "<MyDts>"+
                "<testds>2002-10-10T12:00:00+04:00</testds>"+
        "</MyDts>";
    jsonObj = x2js.xml_str2json( xmlText );
    console.log(jsonObj.MyDts.testds );
```
##Or using the utility function
```javascript
    x2js = new X2JS();

    xmlText = "<MyDts>"+
                "<testds>2002-10-10T12:00:00+04:00</testds>"+
        "</MyDts>";
    jsonObj = x2js.xml_str2json( xmlText );

    console.log(x2js.asDateTime( jsonObj.MyDts.testds ));
```

#Networking Samples
##Parsing AJAX XML response (JQuery sample)
```javascript
$.ajax({
    type: "GET",
    url: "/test",
    dataType: "xml",
    success: function(xmlDoc) {
        var x2js = new X2JS();
        var jsonObj = x2js.xml2json(xmlDoc);
  
    }
});
```
##Loading XML and converting to JSON
```javascript
    function loadXMLDoc(dname) {
        if (window.XMLHttpRequest) {
            xhttp=new XMLHttpRequest();
        }
        else {
            xhttp=new ActiveXObject("Microsoft.XMLHTTP");
        }
        xhttp.open("GET",dname,false);
        xhttp.send();
        return xhttp.responseXML;
    }


    var xmlDoc = loadXMLDoc("test.xml");
    var x2js = new X2JS();
    var jsonObj = x2js.xml2json(xmlDoc);
```

#All available config options
##X2JS instance config options
```javascript
// Available options
    var x2jsOptionsSample = new X2JS({
        // Escaping XML characters. Default is true from v1.1.0+
        escapeMode : true,                              
        // XML attributes prefix. Default is "_"
        attributePrefix : "_",
        // Array access form (none|property). Use property if you want X2JS generate additional property <element>_asArray to access in array form any element
        // Default is none from v1.1.0+
        arrayAccessForm : "none",
        // Handling empty nodes (text|object). 
        // When X2JS found empty node like <test></test> it will be transformed to test : '' for 'text' mode, 
        // or to Object for 'object' mode  
        // Default is 'text'
        emptyNodeForm  : "text",

        // Enable/Disable auxiliary function in generated JSON object to print text nodes with __text/__cdata
        // Default is true
        enableToStringFunc : true,
        
        // Array access paths (array). 
        // Use this option to configure paths to XML elements always in "array form". 
        // You can configure beforehand paths to all your array elements based on XSD or your knowledge
        // about XML structure
        // Every path could be a simple string (like 'parent.child1.child2'), a regex (like /.*\.child2/), or a custom function
        arrayAccessFormPaths : [],

        // Skip empty text tags for nodes with children
        skipEmptyTextNodesForObj : true,

        // Strip whitespaces (trimming text nodes)
        stripWhitespaces : true,

        // DateTime access paths (array). 
        // Use this option to configure paths to XML elements for "datetimes form". 
        // You can configure beforehand paths to all your array elements based on XSD or your knowledge
        // about XML structure
        // Every path could be a simple string (like 'parent.child1.child2'), a regex (like /.*\.child2/), or a custom function
        // Default is empty array
        datetimeAccessFormPaths : []
    });
```
