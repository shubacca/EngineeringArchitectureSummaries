All information in this blog summary is obtained from the [Airbnb Engineering Blog](https://medium.com/airbnb-engineering/prioritizing-home-attributes-based-on-guest-interest-3c49b827e51a)

The variety of property attributes given in a listing don't always provide information on what guests value in those properties. Their opinions are reflected in their messages to the Hosts, the reviews of the property and customer support tickets and listing descriptions. 

**Problem Statement:** Can Airbnb recommend better attributes to Hosts to include in their listings based on Guests' reviews and messages, such that the properties generate more engagement? 

To come up with a creative solution, Airbnb built the **Attribute Prioritization System (APS)** goes through a series of models that streamline this process:

1. Parse the unstructured data contained within reviews, messages, customer support tickets and listing descriptions, and map individual sentences to interesting entities that concern the property;
2. Predict what attributes are important to guests in each type of home (cabins, urban villa, etc.), based on frequency of mentions of those attributes; create a ranked score of the attributes for each home;
3. Recommend to Hosts about amenities that they may need to acquire based on popular requests, or highlighting an existing home attribute that guests tend to comment favorably on.

---
**Listing Attribute Extraction (LATEX) System**
<br>
To effectively parse the intents from the unstructured text contained within guest messages and reviews, Airbnb utilizes a special machine learning system called LATEX.
It consists of two steps: 

1. A named entity recognition (NER) model that identifies important phrases: Airbnb uses [textCNN](chrome-extension://efaidnbmnnnibpcajpcglclefindmkaj/https://arxiv.org/pdf/1408.5882.pdf) to identify text phrases that could fall under 5 categories (amenity, activity, event, specific POI or generic POI). 
2. The second step consists of an entity-mapping module, that maps the important phrases with home attributes in a vector embedding space using cosine similarity. The closest mapped attribute is then used for a confidence score computation. 

The number of times an entity is referenced across different text sources (messages, reviews, customer service tickets, feedback) is then calculated, normalized and aggregated. Home attributes with high frequency scores or more mentions are considered more important. 

---
**What attributes across home types are important to guests?**
<br>


