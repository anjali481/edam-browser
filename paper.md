---
title: 'A reusable tree-based web-visualization to browse EDAM ontology, and contribute to it.'
tags:
- javascript
- bioinformatics
- d3js
- d3.js
- ontology
- visualization
- edamontology
- ontology
authors:
- name: Bryan Brancotte
  orcid: 0000-0001-8669-5525
  affiliation: "1" # (Multiple affiliations must be quoted)
- name: Christophe Blanchet
  affiliation: 2
- name: Hervé Ménager
  orcid: 0000-0002-7552-1009
  affiliation: 1
affiliations:
- name: Centre d'Informatique pour la Biologie, C3BI, Institut Pasteur, Paris, France
  index: 1
- name: French Institute of Bioinformatics, CNRS IFB-Core, Gif-sur-Yvette, France
  index: 2
date: 01 February 2018
bibliography: paper_resources/paper.bib
---

# Summary

## Abstract

Labelling, indexing and describing a resource, whether it is a software, a database, or a service is of a great help when it comes to promoting it to various user communities. As an example, in Bioinformatics, the ELIXIR bio.tools [@ison2015tools] registry contains more than ten thousands software and service entries. In this context, the use of controlled vocabularies to describe the resources is of a paramount importance. This need is addressed by the EDAM Ontology [@ison2013edam] which proposes a controlled vocabulary hierarchically organized around four concepts which are types of data, formats, operations and topics.

We here present the EDAM Browser, a client-side web-based visualization javascript widget to help describing resources, and to facilitate and foster community contributions to EDAM. More precisely, (i) we review related work and features that need to be addressed; (ii) we describe how these features are addressed by the EDAM Browser; finally (iii) we discuss its reusability.

## Related work

When considering web based browsers for ontologies, there are two main platforms (i)BioPortal [@whetzel2011bioportal], and (ii) OLS - Ontology Lookup Service [@jupp2015new]. Here after we describe criteria and there presence absence in the platforms

| id | Feature        | in BioPortal           | in OLS  |
| :-------:|----------- |:-------------:| :-----:|
| 1 |EDAM is present         | ✔       |✔   |
| 2 |Publicly available      | ✔       |  ✔|
| 3 |Handle multiple parents | ✘       |    ✔ |
| 4 |Can be integrated in external websites | ✔       |    ✔ |
| 5 |Facilitate community contribution | limited       |    ✘ |
| 6 |Use edited version of EDAM | ✘       |    ✘ |
| 7 |Local installation | ✔ (as a VM)       |    ✔ (with admin rights) |

##### Criteria 1 and 2

Both platforms integrate EDAM and let not authenticated user browse it.

##### Criteria 3 "Handle multiple parents"

The EDAM ontology, while being represented as a tree is a directed acyclic graph, meaning that a term can have more than one parent. When searching for "Phylogeny" Topic, [BioPortal](http://bioportal.bioontology.org/ontologies/EDAM/?p=classes&conceptid=http%3A%2F%2Fedamontology.org%2Ftopic_0084) displays it at only one position while [OLS](https://www.ebi.ac.uk/ols/ontologies/edam/terms?iri=http%3A%2F%2Fedamontology.org%2Ftopic_0084) indicates that indeed the term has multiple parents. 

##### Criteria 4 "Can be integrated in external websites"

Bioportal allows to integrate [widgets](http://bioportal.bioontology.org/ontologies/EDAM/?p=widgets) such as autocomplete form, graph visualization, and tree visualization. Widgets for OLS can be found in [biojs](http://biojs.io) registry.

##### Criteria 5 "Facilitate community contributions"

BioPortal provides a custom suggestion and bug report mechanism that lets users provide feedback on the contents of the ontology. However, this mechanism, being generic to all ontologies, is not deeply integrated with the development forge used by each of them. OLS, on the other hand, provides only the possibility to contact the authors through email.

##### Criteria 6 "Use custom version of EDAM"

Beyond the regular official releases of the EDAM ontology, the "in-developement" version cannot be browsed with BioPortal nor OLS without a manual and public submission. The publication of submitted releases is usually processed within a few hours.

##### Criteria 7 "Local installation"

Installing locally OLS is [possible](https://www.ebi.ac.uk/ols/docs/installation-guide). Software requirements includes Maven, Neo4J, MongoDB, tomcat, Solr. The installation is thus only for user with administrator rights on their computer, and computers with a certain amount of resources. BioPortal, on the other hand, is available as a [virtual appliance](https://www.bioontology.org/wiki/index.php/Category:NCBO_Virtual_Appliance).

## Main functionalities

In this section, we present how each feature of the previous section is addressed in EDAM Browser.

##### Criteria 1 and 2

The EDAM Browser is publicly available (criteria 1) and does not require authentication (criteria 2). 

##### Criteria 3

Multiple parents are handled (see for [instance](https://ifb-elixirfr.github.io/edam-browser/#topic_0084)). In order to improve readability when a term is selected (1) all the term's positions are shown; and (2) all paths from the root node are highlighted.

![The term Phylogeny has two parents](paper_resources/topics_tree_phylogeny.png)

##### Criteria 4

The ability to integrate the EDAM Browser, and its tree representation is major requirement, in order to help third party websites promoting their resources when labeled with EDAM. Both the autocomplete input field and the tree visualization are re-usable. To highlight this statement we propose a dynamic [demo](https://ifb-elixirfr.github.io/edam-browser/demo.html) showing how it can be integrated, how the user can  interact with the tree, and how to programmaticaly interact with the tree in Javascript. 

##### Criteria 5

Letting users contribute to the ontology improves acceptance by the community by letting them propose changes to the ontology. By contribution, we mean correction of definitions, proposition of new synonyms, or even new terms. The EDAM Browser gives access to a form letting the user propose the changes and then format his/her changes in a github issue ready to be submitted by the user. 

![The edition form](paper_resources/edition_form.png)

![The formated issue in GitHub](paper_resources/github_issue.png)

##### Criteria 6

Using the EDAM Browser to explore an custom ontology is possible, the ontology should be in the following simplified schema which is expressed in [json schema](http://json-schema.org/). A complete version of the schema is accessible [here](ontology.schema.json). 

```json
{
    "$ref": "#/definitions/EDAMBrowserTerm",
    "definitions": {
        "EDAMBrowserTerm": {
            "type": "object",
            "additionalProperties": true,
            "properties": {
                "id": {
                    "type": "string"
                },
                "text": {
                    "type": "string"
                },
                "description": {
                    "type": "string"
                },
                "children": {
                    "type": "array",
                    "items": {
                        "$ref": "#/definitions/EDAMBrowserTerm"
                    }
                }
            },
            "required": [
                "id",
                "text"
            ],
            "title": "EDAMBrowserTerm"
        }
    }
}
```

The user only have to open the EDAM Browser and click on the button labelled _Custom_ at the top of it interface. It then open a form where the user either provide the url where the ontology file can be found, or the file, and also indicate how identifier and text can be accessed in the ontology.

![Providing a custom ontology](paper_resources/custom_ontology.png)

##### Criteria 7

The EDAM Browser can easily be downloaded and run on a personal computer. The user either open index.html and then chose to load an ontology, or start using `python start_edam_stand_alone_browser.py` and the ontology will automatically be loaded.
Once downloaded, the user can also use a custom version of the ontology, including beta version of the next release or a whole new ontology. By default, ontology should be in the [json schema](http://json-schema.org/) presented previously, but the browser can be configured to use a different schema (c.f: hereafter). A validator is available [here](https://jsonschemalint.com/).

## Reusability

The tree-based visualization of the EDAM Browser uses [d3.js](https://d3js.org/) v3 [d3js]. In order to make the tree re-usable we used [Javascript Closures](http://jibbering.com/faq/notes/closures/) following 
[Towards Updatable D3.js Charts](https://www.toptal.com/d3-js/towards-reusable-d3-js-charts)  and
[Towards Reusable Charts](https://bost.ocks.org/mike/chart/). It allows us to create encapsulated charts which are easily customizable. 

The following example comes from the demo and shows how  to visualize the ontology at `media/topic_extended.biotools.min.json` in the div `#tree` with the `Bioinformatics` term initially selected. Note that as the format of the file does not follow the recommendation, we have to override one of the getters to access the identifier.

```javascript
my_tree = interactive_tree()
    //overriding as the identifier by default consider d.id
    .identifierAccessor(function(d){return d.data.uri;})
    .debug(true)
    .tooltipEnabled(true)
    //at first Bioinformatics should be selected
    .initiallySelectedElementHandler(function(d){
        return d.data.uri==="http://edamontology.org/topic_0091"
    })
    //call the demo (next step you see) when the tree have been loaded
    .loadingDoneHandler(demo)
;
// pre-draw/build chart in div
d3.select("#tree").call(my_tree);
//indicated where the data can be found
my_tree.data_url("media/topic_extended.biotools.min.json");
```


## Conclusion

We here presented the EDAM Browser, which allows users to browse this ontology, and resource owners to easily display the terms used to label the resource. We enumerated seven major features and showed that state of the art web based ontology browsers do not provide all of them while EDAM Browser does. Finally we described how it can be easily re-used in any third part website.

# References