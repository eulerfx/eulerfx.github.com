---
layout: post
title: "Introducing BASE"
date: 2012-09-24 21:20
comments: true
categories: [semantic-web, ontology]
published: false
---



# Outline
- Introduce problems with technology and information. Mention Information Governance, reference by Obama, etc.
	- Tribal knowledge
	- Shift from data to knowledge
	- Common language <-> Ubiquitous Language
	- Ontology
	- "Knowledge is power" -Bacon
	- Keynote by CTO of DoD
	- Ontology is beyond SOA w/ Canonical Model
- Ontology Maps and Modules (like DDD context mapping)
	- Common sense ontology: http://cyc.com/
	- Common Sense w/ Cyc
	- BIAN
- While Business (a SME) knows the answers, an Architect, trained with a systems approach, can provide good questions for the conversational scripts.
- CSDS is iterative and agile
- Refer to MDA pitfals

- BASE
	- Goals
		- Create initial semantic models
		- Provide semantic support for development (via CSDS)
		- Establish collaborative playground for creating workflows and services
		- Add semantic layer to ESB
		- Evolve SOA to Semantic SOA
	- Parts
		- Standard platform for sync and async processing of business events
			- SAGA, Request/Acknowledge/Poll
		- Integration with JMS (Mule, ActiveMQ)
		- Predefined, skeleton models followed by specifics (pg.8)
		- Semantic integration option connects to data sources to discover details and relationships. Extracted information will be mapped to ontology, such as FIBO.
			- Does not compete with CMDB
		- "BASE allows business analysts and developers to collaborate in the development of new components within an existing semantic SOA model."
		- Workflow -> decision model -> rule family. (pg.12)
		- "BASE creates a unified semantic information landscape and with the Conversational Semantic Decision Support helps to establish and manage rule-based workflow processes."
		- "BASE offers a built-in rules engine, which is implemented as decision tables and is connected to the semantic model.".
		- "One of the benefits of the semantic approach is its focus on information with the significant shift to providing the knowledge of handling data together with data versus traditional business logic placed in multiple applications dealing with data. This approach removes the biggest problem of using rules engines: the struggle of supplying data for the rules."
		- "The decision table can use multiple conclusions and serve as a data transformation table, which transforms one data set into another data set." (pg.18)
		- Adaptive/Situational Processing
			- "The Semantic Model in BASE allows a business analyst to connect multiple alert stories into a situational description and detect a coming problem."
			- "This is one more side of the multi-dimensional effort of transferring “tribal knowledge” into more precise forms of the semantic world."			
		- Semantic Reporting: "This layer will allow developers to introduce a semantic listener and provide opportunities for subject matter experts to talk business terms while expressing their interest in specific reports based on enterprise messages."
		- "BASE helps content providers capture events, reports, or stories with auto-transformation into a semantic graph."
		- "While BASE helps with the transition to semantic cloud architecture, a fast growing set of the powerful tools like Fluid Operations [14], Allegro Graph [15], Knoodle [16], and more, is making the way to standardize semantic operations in this Linked Data universe."
	- Benefits
		- Enterprise Data Integration (pg. 28)
		- Content Management, Enrichment, and Analysis










# Quotes
- "Managing a company’s information must be one of the core competency areas of a company, not a subject for outsourcing."
- "The primary purpose is setting a common ground where business analysts and developers can collaborate on real business tasks."
- "SMEs are more than busy with their daily routine and will not change their ways...unless you offer immediate benefits."
- "In a semantically rich environment, there is no need for complex tools."
- "Current tools and development practices often assume that developers have correct models of enterprise systems and relationships in their heads. This assumption is not true." - What about Agile?
- "In real life everything is inter-connected, but in our descriptions of real life, we lack the majority of these connections."





# Key Points
- "Besides collecting the key vocabulary, ontology picks up the relationships between the keywords effectively building semantically rich, much more meaningful model of the content."
- "BASE offers to business an easy entrance and a playground to collaborate with IT, while placing the seeds of semantic technology in the current business ground and transitioning to a Semantic Cloud Architecture."
- "Naming conventions play an essential role in reducing translation layers in development and analysis."
- "A semantic approach improves development and prevents duplication."
- "The most common problem with rules engines is in the handling of data within the rules. For example, JBoss Drools requires a developer to do massive data drilling with Java code before any rules can actually apply."
- "BASE uses semantic approach to shift this paradigm from “applications know how to handle data” to “data know how to handle data’."
- "the story of the integration evolution, from point-to-point to centralized, and further to canonical interfaces with the semantic layer, which connects multiple business dialects." (pg.23)
- "Canonical Data Model (CDM) and Common Vocabulary (Ontology) help developers in naming the services in a non-ambiguous way, so the service names can be understood by people as well as computer systems."




# Components
- Semantic technology
- Triple store
- CSDS




# Capabilities
- Workflows
- Rules
- Decision tables




# Technology
- Liferay
- Triple-store
- SPARQL
- Service bus (Mule,EasyMQ)
- RDF
- OWL
- BPMN



# Questions
- Is the tool *for* SMEs or *for* developers or both? What is a typical set of interactions between developers and SMEs? For example, SMEs designs workflows and provides rules; developer provides implementation of required functionality.
- "The trickiest part is collecting complete information related to a company and its business." - I would think the trickiest part is putting that information to use once it is collected and consolidated.
- How, if so, can this be applied in a smaller enterprise?
- How does CSDS help SME? How does it bring immediate benefits?
- "One of the current development trends is a shift to rule-based applications." - I've heard the contrary, that rule engines have not attained expected levels of adoption. Can you provide examples of some rule engines? Drools?
- Does BASE use the Rete algorithm?
- "In this case, the results of analysis are automatically transformed into a decision table to drive service mediation at run-time and also become part of the “localized ontology” that provides the mapping between the conceptual ontological model to proprietary models and values of legacy systems." - At run-time? What is the nature of this mediation?
- A specified goal of BASE is "Standardize a RESTful API for multiple systems and 3-rd party developers" - what is the nature of this goal?
- On page 8, the skeleton model diagram, what is the meaning of the arrows?
- On page 10, "The semantic model can be understood by a computer. This understanding enables new opportunities for collaborative work of subject matter experts (SME) and computer programs in business development and transferring “tribal knowledge” into decision making systems.". How so? I believe once the information is stored, the biggest challenge is delivering that information in a facilitating manner.
- If implementations are provided by developers, is there a way to run workflows? "Once rules and rule families are defined in a precise and consistent way they can be automatically transformed into code, saving development time." - How does this transformation process work?
- On pg.18 - "In this world of linked data any application or a rule, which uses a data attribute, will automatically know how about major data handlers, because “data know how to handle data”." - what are data handlers? What are major data handlers?
- On pg. 19 it is stated that an architect/developer provides the retrieval and validaiton methods. How are these methods provided? In what language? A DSL or Java?
- How does this connect with Mule studio?
- In situational event processing, how is semantic information extracted or parsed from the event payload? By use of consistent and meaningful service names? By use of formats such as on page 25?
- "Current tools and development practices often assume that developers have correct models of enterprise systems and relationships in their heads. This assumption is not true." - What about Agile?





# References
- W3C on Large Triple Stores, http://www.w3.org/wiki/LargeTripleStores

- Realizing Efficiency & Interoperability: SOA & Semantic Technology in the Business Mission Area (BMA), U.S. DoD, Dennis E. Wisnosky, CTO, DoD (See Mr. Wisnosky’s 2011 Semantic Technology Keynote here)

- Integration-Ready Architecture and Design, Jeff (Yefim) Zhuk, Cambridge University Press, A book on Software and Knowledge Engineering

- Measuring IT Tactical Spending to Provide Transparency, Gartner Research

- Rules Collector, Yefim Zhuk/Boeing, From “tribal knowledge” to rules and rule-based applications

- World Wide Web Consortium, W3C, http://w3c.org

- RDF Services, http://www.w3.org/1999/11/02-RDFServices/

- Open Group Service-oriented architecture ontology, http://www.architecting-the-enterprise.com/blog/2011/01/the-open-group-service-oriented-architecture-ontology/

- Banking Industry Architecture Network (BIAN), http://www.bian.org/content/about_bian/index_en.html

- Knowledge-Driven Architecture, Yefim Zhuk, Streamlining development and driving applications with business rules & scenarios

- Open Ontology Repository (OOR) Initiative, http://ontolog.cim3.net/cgi-bin/wiki.pl?OpenOntologyRepository

- Cycorp, Inc. Knowledge Engineering and Common Sense Reasoning, http://cyc.com

- Enterprise Data Management Council (EDMC) and OMG, http://www.edmcouncil.org/PDFs/20110330.OMG.EDM.Relationship.pdf

- Adaptive Robot System with Knowledge-Driven Architecture, Yefim Zhuk, On-the-fly translations of situational requirements into adaptive robot skills

- Collaborative security and decision making in service-oriented environment, Yefim Zhuk/Boeing, Collaboration of multiple services and SMEs while processing many informational streams to optimize a strategy to achieve one or more goals.

- From Business As Usual to Knowledge-Driven Architecture, Jeff (Yefim) Zhuk

- Dublin Core, Meta Data Initiative, offers common ontology, http://dublincore.org

- Financial Industry Business Ontology (FIBO), initiated by EDM Council, www.edmcouncil.org

- Semantic Technology 2012 Conference, San Francisco, http://semtechbizsf2012.semanticweb.com

- Fluid Operations, Inc., Open Platform for Linked Data Solutions, http://www.fluidops.com/

- Allegro Graph, RDF DB and a set of semantic tools, http://www.franz.com/agraph/allegrograph/

- Knoodle, Content authoring and management solutions, http://www.knoodle.com

- http://javaschool.com/about/publications.html

- http://www.amazon.com/Decision-Model-Framework-Technology-Management/dp/1420082817