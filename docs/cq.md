## Competency Questions

FRAPO can be used for answering several questions related to administrative information about grant funding and research projects.
In the following subsections, some of them are introduced together with their respective SPARQL queries. 

The prefixes that are used in all the SPARQL queries provided below are defined as follows:

    PREFIX frapo: <http://purl.org/cerif/frapo/>
    PREFIX scoro: <http://purl.org/spar/scoro/>
    PREFIX foaf: <http://xmlns.com/foaf/0.1/>
    PREFIX pro: <http://purl.org/spar/pro/>

### CQ1

Which funding awards or scholarships are granted by a university?

    SELECT ?university ?universityName ?award ?awardName
    WHERE {
        ?university a frapo:University ;
            frapo:awards ?award .
        OPTIONAL { ?university foaf:name ?universityName . }
        OPTIONAL { ?award foaf:name ?awardName . }
    }

### CQ2

Which investigation is funded by a specific scholarship?

    SELECT ?fundingAward ?awardName ?investigation
    WHERE {
        ?fundingAward a frapo:Scholarship ;
            frapo:funds ?investigation .
        ?investigation a frapo:Investigation .
        OPTIONAL { ?fundingAward foaf:name ?awardName . }
    }

### CQ3

What outputs are produced by an investigation?

    SELECT ?investigation ?outputDocument
    WHERE {
        ?investigation a frapo:Investigation ;
            frapo:hasOutput ?outputDocument .
    }

### CQ4

Which funding awards granted by a university finance an investigation that produced a document to which an author is affiliated?

    SELECT ?person ?university ?award ?investigation ?document
    WHERE {
        ?person a foaf:Person ;
            pro:holdsRoleInTime ?roleInTime .
        ?roleInTime pro:relatesToOrganization ?university ;
            pro:relatesToDocument ?document .
        ?university a frapo:University ;
            frapo:awards ?award .
        ?award frapo:funds ?investigation .
        ?investigation frapo:hasOutput ?document .
    }

### CQ5

What specific contributions are made by a person toward an output document resulting from a funded investigation?

    SELECT ?person ?contributionType ?effort ?document ?investigation
    WHERE {
        ?person a foaf:Person ;
            scoro:makesContribution ?situation .
        ?situation a scoro:ContributionSituation ;
            scoro:withContribution ?contributionType ;
            scoro:relatesToEntity ?document .
        ?investigation a frapo:Investigation ;
            frapo:hasOutput ?document .
        OPTIONAL { ?situation scoro:withContributionEffort ?effort . }
    }