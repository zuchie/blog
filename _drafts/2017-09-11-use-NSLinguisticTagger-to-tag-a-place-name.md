---
layout: post
title:  Tag a place name by using NSLinguisticTagger, and its drawbacks
date:   2017-09-11 01:34:00 -0700
comments: true
categories: Swift 
---

![Place Name Tagger](/assets/img/placeNameTagger/place_tagger.jpg){:height="200px" width="300px" }

I want to make a chatbot to do simple communication and API queries. In order to make it able to "understand" what I'm saying I need to use Natural Language Processing to let it parse the sentences that I feed to it. I turned to Apple's `NSLinguisticTagger` which is a class for NLP based on the foundation of Core ML.
<!--more-->

**Implementation**

The `NSLinguisticTagger` class is intuitive to use. A few lines of code would make it work. Here is a simple implementation for test:
```swift
  func nlp(_ text: String) {
    let text = text 
    let tagScheme = NSLinguisticTagSchemeNameType
    let taggerOptions: NSLinguisticTagger.Options = [.omitWhitespace, .omitPunctuation, .omitOther, .joinNames]
    let tagger = NSLinguisticTagger(tagSchemes: [tagScheme], options: Int(taggerOptions.rawValue))
    tagger.string = text
    let range = NSRange(location: 0, length: text.utf16.count)
    
    tagger.enumerateTags(in: range, scheme: tagScheme, options: taggerOptions) {
      tag, tokenRange, _, _ in
      let token = (text as NSString).substring(with: tokenRange)
      print("\(token): \(tag)")
    }
  }
```
Let's give it a try, hope it could recognize the place name San Francisco:

`nlp("Weather in San Francisco")`

We got what we want, it indeed recognized the place name, bravo!

```
Weather: OtherWord
in: OtherWord
San Francisco: PlaceName
```

**Drawback**

However, I found certain sentence patterns would fail the test. For example:

`nlp("Weather in San Francisco tomorrow")`

It gave:

```
Weather: OtherWord
in: OtherWord
San Francisco tomorrow: PersonalName
```


Also, I found this [thread](https://stackoverflow.com/questions/14938867/objective-c-nslinguistictagger-new-york-vs-new-york) addressing the parsing difference caused by capitalization. 

Hmmm, looks like `NSLinguisticTagger` is restrictive on the input. One has to pre-process the "improper" inputs to make it work. Not the one I was looking for for my case. Search begins for other linguistic parsers to the rescue.


