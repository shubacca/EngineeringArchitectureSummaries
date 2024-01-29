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
To determine what attributes are desirable for each kind of home (lodge, urban condo, hotel room etc.), a unique ranking of attriutes is created using a model. Based on home's properties like location, type, area, capacity, luxury level, the model predicts how many times an attribute will be mentioned in segments of text (messages, reviews and customer service tickets). Ordering these frequencies of mentions in decreasing order helps create a ranked list of importance scores for all attributes of a home. 

For example, an urban apartment has high importance scores given to mentions of restaurant, parking, city view, kitchen and balcony. 

One may argue that aggregating these attribute mentions across home types could have easily given a ranked list of attributes. However, this would not take into account the transient nature of comments by guests, and the ever-increasing corpus of data. As such an inference model was developed that uses the raw frequency data to infer the expected frequency for a segment. 

With this knowledge, Airbnb can recommend Hosts about the most mentioned attributes, such as amenities that are most often requested. Hosts can also get an idea on what attributes to highlight and mention in their Home listing page.  

The last piece of the puzzle is figuring out what attributes are already present in the Home, without asking the Host about them. Guest feedback for amenities that have been used by Guests, can provide a listing of the important amenities present. Trusted third-party 

