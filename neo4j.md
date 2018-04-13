# Neo4j
## Noe4j in practice
[Problems when working with neo4j](https://hackernoon.com/life-after-1-year-of-using-neo4j-4eca5ce95bf5)
## Installation
### Docker
Create folders for docker volumes.
```shell
mkdir -p neo4j/wykop/data  neo4j/wykop/logs  neo4j/wykop/plugins
chmod -R a+w neo4j
```
Second line is a nasty hack but without it neo will throws "/logs/debug.log (Permission denied)". It is possible to set correct permisions (creating group for neo) but I don't want waste time now on this.

Run container
`docker run -p 7474:7474 -p 7687:7687 -e NEO4J_dbms_security_procedures_unrestricted=apoc.*  -v $HOME/neo4j/wykop/data:/data -v $HOME/neo4j/wykop/logs:/logs -v $HOME/neo4j/wykop/plugins:/plugins neo4j:3.3.4
Active database: graph.db`

`-e NEO4J_dbms_security_procedures_unrestricted=apoc.*` is required to use apoc without [problems](https://neo4j.com/developer/kb/explanation-of-error-procedure-is-not-available-due-to-having-restricted-access-rights-check-configuration/).

Befor starting change password. Best way to do this is to use browser: `http://localhost:7474`

#### APOC
To have access to apoc procedures download jar related to version of running neo4j and copy it to plugins folder.
[APOC releases](https://github.com/neo4j-contrib/neo4j-apoc-procedures/releases)

To verify installation run:
```cypher
CALL dbms.functions() YIELD name
WHERE name STARTS WITH 'apoc.'
RETURN count(name)
UNION
CALL dbms.procedures() YIELD name
WHERE name STARTS WITH 'apoc.'
RETURN count(name);
```
Example result:
```csv
count(name)
188
253
```

### Cypher shell
*Doesn't work :(*
Because browser tool have performance issues it is better to use command line tool. To use it with running neo4j in docker container:
```bash
 docker exec -ti $(docker ps|grep neo4j|awk '{ print $1}') /var/lib/neo4j/bin/neo4j-shell
```

## Cypher

- [Detail description of commands](https://neo4j.com/docs/developer-manual/current/cypher/clauses/)
- [Good explenation of MERGE and how to use it](https://www.graphgrid.com/using-neo4j-cypher-merge-effectively/)
- [MERGE with examples](https://graphaware.com/neo4j/2014/07/31/cypher-merge-explained.html)
- [Cypher cheat sheet](https://neo4j.com/docs/cypher-refcard/current/)
- [General documentation](https://neo4j.com/docs/developer-manual/current/introduction/graphdb-concepts/)

### Inserting data
```cypher
CREATE (:User {name:"User1", links:0})
CREATE (:User {name:"User2", links:0})
CREATE(:Link {id:"21245", url:"http://google.com", name:"Google", up: 0, down: 0, votes:0})
```
This creats nodes. String after colon define node type (User, Link). Nodes parameters are defined as key-value map. Those nodes are not unique. We can add multiple "User1" nodes.
### Match (select) command
```cypher
MATCH(u:User) RETURN *
MATCH(u:User) DETACH DELETE u
MATCH(u:User) WHERE u.name="User1" DETACH DELETE u
MATCH(u:User {name:"Tom"}) WHERE u.links < 4 RETURN u.name, u.links
```
Match have two parts. First responsivble for selecting nodes and second responsible for defining what to do with results. We can in example return found nodes or remove them.
When we know value of parameter we can put it in brackets. In other case we can use WHERE cause
```cypher
MATCH(u:User {name:"Tom", links:3}) RETURN u.name, u.links
MATCH(u:User {name:"Tom"}) WHERE u.links < 4 RETURN u.name, u.links
```
### Create relation between existing nodes
```cypher
MATCH (u:User {name:"User1"}), (l:Link {name:"Google"})
CREATE (u)-[:UP_VOTE]->(l)
```

**Warning!** Be aware about indexes and proformance of this query. (https://stackoverflow.com/questions/20456002/adding-relationship-to-existing-nodes-with-cypher)
It is possible to create indexes to avoid performance problems.
```cypher
CREATE INDEX ON :User(name)
CREATE INDEX ON :Link(name)
```
### Merge
Merge create or update existing node. This will create new user node.
```cypher
MERGE(u:User{name:"Tom", links:0})
```
It is importante to notice that it try to match both parameters, name and link. If I have already Tom node but with links equals 2 it will add new one with name Tom and links 0. 
To update params of exisitng node use `ON MATCH` and `ON CREATE` to set default value if node doesn't exists.
```cypher
MERGE(u:User{name:"Tom"})
ON CREATE SET u.links=0
ON MATCH SET u.links = u.links+1
```
We may also update properties when merging relation. In this case if nodes exists we will increase vote related valuse.
```cypher
MATCH (u:User {name:"Tom"}), (l:Link {name:"Google"})
MERGE (u)-[:UP_VOTE]->(l)
ON MATCH SET u.links=u.links+1
ON MATCH SET l.up=l.up+1
RETURN *
```
### Displaying relation label/type in results
```cypher
match (p:Page)<-[r]-(u:User) return p.id, u.name, TYPE(r)
```
### Using WITH statement to "pipe" result of one query to another (?)
With and create
```cypher
CREATE (
 flight:Flight {
  code:"AA920",   
  carrier:"American Airlines", 
  duration:305,   
  source_airport_code:"LAX", 
  departure:505,   
  destination_airport_code:"JFK", 
  arrival:990
 }
)WITH flight
MATCH 
 (source:City {name:"Los Angeles"}),   
 (destination:City {name:"New York"})
CREATE (source)-[:HAS_FLIGHT]->  (flight)-[:FLYING_TO]->(destination);
```
With and match
```cypher
match (:User {name:"DonutDaddy"})-[:UP_VOTE*3]-(p:Page) with p 
match (p)-[:HAS_TAG]-(t:Tag) return *
```
![match with match example](https://github.com/lzenczuk/notes/blob/master/n4j_match_with_match.png)
### Matching nodes in specific distance
One step
```cypher
match path = (u:User {name:"DonutDaddy"})-[:UP_VOTE*2]-(su:User) return path
```
![one step](https://github.com/lzenczuk/notes/blob/master/n4j_one_step.png)

Two steps
```cypher
match path = (u:User {name:"DonutDaddy"})-[:UP_VOTE*4]-(su:User) return path
```
![two steps](https://github.com/lzenczuk/notes/blob/master/n4j_two_steps.png)

One or two steps
```cypher
match path = (u:User {name:"DonutDaddy"})-[:UP_VOTE*2..4]-(su:User) return path
```
![one or two steps](https://github.com/lzenczuk/notes/blob/master/n4j_one_or_two_steps.png)

Pages three up votes from user
```cypher
match path = (u:User {name:"DonutDaddy"})-[:UP_VOTE*3]-(p:Page) return path
```
![three up votes from user](https://github.com/lzenczuk/notes/blob/master/n4j_pages.png)
### Substrion function
In this example substring allow to shorten description string.
```cypher
match (p:Page) RETURN p.id, substring(p.description, 80), p.up_votes, p.down_votes
```
### Collect - agregate parameter to list
```cypher
match (p:Page)-[:HAS_TAG]->(t:Tag) RETURN p.id, collect(t.name)
```
In result we getting table containing page id and list of tag names.
### Sorting results
```cypher
match (t:Tag) return t.name, t.pages order by t.pages desc
```
### Count as group operation
```cypher
match (u:User)-[:UP_VOTE]->(:Page)-[:HAS_TAG]->(t:Tag) 
return u.name as user_name, t.name as tag_name, count(t.name) as c 
order by user_name, c desc
```
### Standard deviation and average
```cypher
match (u:User) 
RETURN stDev(u.up_votes), avg(u.up_votes), 
stDev(u.down_votes), avg(u.down_votes);
```
### Add label to existing node
```cypher
match (n {name:"Alex"})
set n :Developer
return n
```
## Constraints and indices
Constraints and indices are defined on labels. It is **good practice** to add constrains before adding nodes.
### Unique constrain
```cypher
CREATE CONSTRAINT ON (city: City) ASSERT city.name IS UNIQUE;
```
### Indexes
It is good practice to have index on property that we plane to use often in MATCH statement.
```cypher
CREATE INDEX ON :City(country);
MATCH (c:City{country:"United States of America"}) RETURN c.name as City;
```
## APOC
### Help - IMPORTANT
Best source of knwoledge about apoc procedures and their signatures.
```cypher
call apoc.help("apoc")
```
### Corect properties names for yield
YIELD require to use properties names like in signature. In example yield in pageRank expects node and score, even that we know that node is a page.
```cypher
match (page:Page) with collect(page) as pages
call apoc.algo.pageRank(pages) yield node, score
return node.id, substring(node.description, 0, 60), score
order by score desc;

match (u:User) with collect(u) as users
call apoc.algo.pageRank(users) yield node, score
return node.name,  score
order by score desc;
```
