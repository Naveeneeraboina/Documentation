"Eclipse Ditto" is about providing access to IoT devices via the digital twin pattern. 
In order to provide structured APIs for different heterogeneous devices Ditto defines a lightweight JSON based model.


Thing:
------
Things are very generic entities and are mostly used as a “handle” for multiple features belonging to this Thing.

Examples:

Physical Device: a lawn mower, a sensor, a vehicle, a lamp.
----------------
Virtual Device: a room in a house, a virtual power plant spanning multiple power plants, 
the weather information for a specific location collected by various sensors.

A Thing may contain a definition. The definition can also be used to find Things. 
The definition is used to link a thing to a corresponding model defining the capabilities/features of it.

---------------
Removing Ditto:
---------------
sudo rm -r ditto

--------------------------
listing the docker images:
--------------------------
sudo docker images -a

-----------------------
Deleting docker images:
-----------------------
sudo docker rmi $(sudo docker images -a -q)

sudo docker image prune -a


---------------------------
Deleting docker containers:
---------------------------
sudo docker rm -f $(sudo docker ps -qa)


---------------
Building Ditto:
---------------
clone the Ditto source code repository from GitHub:
---------------------------------------------------
git clone https://github.com/eclipse/ditto.git

1)git clone https://github.com/eclipse/ditto

This will create a ditto folder in the current working directory and clone the whole repository into that folder.

after cloning the repository, change the current branch into release-2.4
------------------------------------------------------------------------
->git status

->git checkout deployment/docker/nginx.htpasswd

->git checkout release-2.4


Starting the Ditto Build Process:
---------------------------------

2)eraboinanaveen@IBS-LAP-371:~/ditto$ mvn clean install

This will build all libraries, Docker images and example code.Docker will need to download all the base images that Ditto is relying on.

3)eraboinanaveen@IBS-LAP-371:~/ditto$ sh build-images.sh
build-images.sh: 22: Bad substitution
build-images.sh: 26: Syntax error: "(" unexpected

steps to solve Bad substitution:
--------------------------------

i)chmod +x build-images.sh

ii)./build-images.sh


install the docker-compose:
---------------------------
sudo apt  install docker-compose

stop the mongo by using this cmd:
---------------------------------
i)ps -ef|grep mongo

ii)sudo kill -9 1357


Running Ditto:
--------------
Eclipse Ditto :: Deployment:
----------------------------
click on "starting via Docker and Docker-Compose"

--------------------
Start Eclipse Ditto:
--------------------
eraboinanaveen@IBS-LAP-371:~/ditto/deployment/docker$ docker-compose up -d

eraboinanaveen@IBS-LAP-371:~/ditto/deployment/docker$ docker ps 

eraboinanaveen@IBS-LAP-371:~/ditto/deployment/docker$ docker ps -a


Configure nginx:    //no need to follow these steps
----------------
openssl passwd -quiet
 Password: ditto
 Verifying - Password: ditto
 
openssl passwd -quiet
Password:welcome@12
Verifying - Password:welcome@12
qZ2P76Agw/lM.


Append the printed hash in the nginx.httpasswd file placing the username:
-------------------------------------------------------------------------
ditto:Jcsauh6A3PAtA


---------------
create a thing:(POST/things)
----------------------------
POST> http://localhost:8080/api/2/things?namespace=eclipse:ditto

curl -X POST "http://localhost:8080/api/2/things?namespace=com.example.namespace" -H "accept: application/json" -H "Authorization: Basic ZGl0dG86ZGl0dG8=" -H "Content-Type: application/json" -d "{\"definition\":\"com.acme:coffeebrewer:0.1.0\",\"attributes\":{\"manufacturer\":\"ACME demo corp.\",\"location\":\"Berlin, main floor\",\"serialno\":\"42\",\"model\":\"Speaking coffee machine\"},\"features\":{\"coffee-brewer\":{\"definition\":[\"com.acme:coffeebrewer:0.1.0\"],\"properties\":{\"brewed-coffees\":0}},\"water-tank\":{\"properties\":{\"configuration\":{\"smartMode\":true,\"brewingTemp\":87,\"tempToHold\":44,\"timeoutSeconds\":6000},\"status\":{\"waterAmount\":731,\"temperature\":44}}}}}"

output:
-------
{
    "thingId": ":d908aca7-a29e-4387-a782-b162daa16ebc",
    "policyId": ":d908aca7-a29e-4387-a782-b162daa16ebc",
    "definition": "com.acme:coffeebrewer:0.1.0",
    "attributes": {
        "manufacturer": "ACME demo corp.",
        "location": "Berlin, main floor",
        "serialno": "42",
        "model": "Speaking coffee machine"
    },
    "features": {
        "coffee-brewer": {
            "definition": [
                "com.acme:coffeebrewer:0.1.0"
            ],
            "properties": {
                "brewed-coffees": 0
            }
        },
        "water-tank": {
            "properties": {
                "configuration": {
                    "smartMode": true,
                    "brewingTemp": 87,
                    "tempToHold": 44,
                    "timeoutSeconds": 6000
                },
                "status": {
                    "waterAmount": 731,
                    "temperature": 44
                }
            }
        }
    }
}

----------
Get thing:(GET/things)
----------
http://localhost:8080/api/2/things

curl -X GET "http://localhost:8080/api/2/things" -H "accept: application/json" -H "Authorization: Basic ZGl0dG86ZGl0dG8="

--------------------------
Retrieve a specific thing:(GET/things/{thingId}
--------------------------
http://localhost:8080/api/2/things/:7f5a05ec-f54f-4830-9158-20722d3855cf

---------------------------------------------
Create or update a thing with a specified ID:(PUT/things/{thingId}
---------------------------------------------

i have changed the location(Berlin, main floor) to (Hyd)
--------------------------------------------------------
PUT> http://localhost:8080/api/2/things/:7f5a05ec-f54f-4830-9158-20722d3855cf


curl -X POST "http://localhost:8080/api/2/things?namespace=ditto" -H "accept: application/json" -H "Authorization: Basic ZGl0dG86ZGl0dG8=" -H "Content-Type: application/json" -d "{\"definition\":\"com.acme:coffeebrewer:0.1.0\",\"attributes\":{\"manufacturer\":\"ACME demo corp.\",\"location\":\"Berlin, main floor\",\"serialno\":\"42\",\"model\":\"Speaking coffee machine\"},\"features\":{\"coffee-brewer\":{\"definition\":[\"com.acme:coffeebrewer:0.1.0\"],\"properties\":{\"brewed-coffees\":0}},\"water-tank\":{\"properties\":{\"configuration\":{\"smartMode\":true,\"brewingTemp\":87,\"tempToHold\":44,\"timeoutSeconds\":6000},\"status\":{\"waterAmount\":731,\"temperature\":44}}}}}"

----------------------------------------------------------
Patch a thing with a specified ID(PATCH/things/{thingId}):
----------------------------------------------------------
To make changes that only affect parts of the existing thing, e.g. add some attribute and delete a specific feature property, the content of the request body could look like this:

"in Postman,add the header as(Content-Type || application/merge-patch+json)"

PATCH> http://localhost:8080/api/2/things/:7f5a05ec-f54f-4830-9158-20722d3855cf

-> i have added the manufacturingYear in this thing

{
  "attributes": {
    "manufacturingYear": "2020"
  },
  "features": {
    "water-tank": {
      "properties": {
        "configuration": {
          "smartMode": null,
          "tempToHold": 50,
        }
      }
    }
  }
}


------------------------
Delete a specific thing:(DELETE/things/{thingId})
------------------------
http://localhost:8080/api/2/things/:7f5a05ec-f54f-4830-9158-20722d3855cf


--------------------------------------------
Retrieve the definition of a specific thing:(GET/things/{thingId}/definition)
--------------------------------------------
GET> http://localhost:8080/api/2/things/:7f5a05ec-f54f-4830-9158-20722d3855cf/definition


---------------------------------------------------
Create or update the definition of a specific thing:(PUT/things/{thingId}/definition)
---------------------------------------------------
PUT> http://localhost:8080/api/2/things/:7f5a05ec-f54f-4830-9158-20722d3855cf/definition

JSON:
"example:test:definition"


-----------------------------------------
Patch the definition of a specific thing:(PATCH/things/{thingId}/definition)
-----------------------------------------
PATCH> http://localhost:8080/api/2/things/:7f5a05ec-f54f-4830-9158-20722d3855cf/definition

"in Postman,add the header as(Content-Type || application/merge-patch+json)"

JSON:
"ditto:test:definition"

------------------------------------------
Delete the definition of a specific thing:(DELETE/things/{thingId}/definition)
------------------------------------------
DELETE> http://localhost:8080/api/2/things/:7f5a05ec-f54f-4830-9158-20722d3855cf/definition


----------------------------------
Retrieve the policy ID of a thing:(GET/things/{thingId}/policyId)
----------------------------------
GET> http://localhost:8080/api/2/things/:7f5a05ec-f54f-4830-9158-20722d3855cf/policyId


--------------------------------
Update the policy ID of a thing:(PUT/things/{thingId}/policyId)
--------------------------------
PUT> http://localhost:8080/api/2/things/:7f5a05ec-f54f-4830-9158-20722d3855cf/policyId

JSON:
 "eclipse:ditto-policy"
 
 
-------------------------------
Patch the policy ID of a thing:(PATCH/things/{thingId}/policyId)
-------------------------------
PATCH> http://localhost:8080/api/2/things/:7f5a05ec-f54f-4830-9158-20722d3855cf/policyId

"in Postman,add the header as(Content-Type || application/merge-patch+json)"

JSON:
 "eclipse:ditto-policy"
 
 
----------------------------------------  
List all attributes of a specific thing:(GET/things/{thingId}/attributes)
----------------------------------------
GET> http://localhost:8080/api/2/things/:d908aca7-a29e-4387-a782-b162daa16ebc/attributes


------------------------------------------------------------
Create or update all attributes of a specific thing at once:(PUT/things/{thingId}/attributes)
------------------------------------------------------------
PUT> http://localhost:8080/api/2/things/:d908aca7-a29e-4387-a782-b162daa16ebc/attributes



JSON:
{
  "manufacturer": {
    "name": "Panasonic",
    "location": "Delhi"
  },
  "coffeemaker": {
    "serialno": "42",
    "model": "Speaking coffee machine"
  }
}


-----------------------------------------
Patch all attributes of a specific thing:(PATCH/things/{thingId}/attributes)
-----------------------------------------
PATCH> http://localhost:8080/api/2/things/:d908aca7-a29e-4387-a782-b162daa16ebc/attributes

"in Postman,add the header as(Content-Type || application/merge-patch+json)"

JSON:
{
  "manufacturer": {
    "name": "Vitals",
    "location": "Noida"
  },
  "coffeemaker": {
    "serialno": "42",
    "model": "Speaking coffee machine"
  }
}


--------------------------------------------------
Delete all attributes of a specific thing at once:(DELETE/things/{thingId}/attributes)
--------------------------------------------------
DELETE> http://localhost:8080/api/2/things/:d908aca7-a29e-4387-a782-b162daa16ebc/attributes



--------------------------------------------------
Retrieve a specific attribute of a specific thing:(GET/things/{thingId}/attributes/attributepath)
--------------------------------------------------
GET> http://localhost:8080/api/2/things/:d908aca7-a29e-4387-a782-b162daa16ebc/attributes/manufacturer


----------------------------------------------------------
Create or update a specific attribute of a specific thing:(PUT/things/{thingId}/attributes/attributepath)
----------------------------------------------------------
PUT> http://localhost:8080/api/2/things/:d908aca7-a29e-4387-a782-b162daa16ebc/attributes/manufacturer/name


In order to put the name field of an manufacturer attribute, the full path would be /things/{thingId}/attributes/manufacturer/name


JSON:
 {
    "name": "VitalsIOT"
}


-----------------------------------------------
Patch a specific attribute of a specific thing:(PATCH/things/{thingId}/attributes/attributepath)
-----------------------------------------------
PATCH> http://localhost:8080/api/2/things/:d908aca7-a29e-4387-a782-b162daa16ebc/attributes/manufacturer

"in Postman,add the header as(Content-Type || application/merge-patch+json)"

JSON:
 {
    "location": "Delhi"
}


------------------------------------------------
Delete a specific attribute of a specific thing:(DELETE/things/{thingId}/attributes/attributepath)
------------------------------------------------
DELETE> http://localhost:8080/api/2/things/:d908aca7-a29e-4387-a782-b162daa16ebc/attributes/manufacturer



Features: (Structure the features of your things)
--

List all features of a specific thing:(GET/things/{thingId}/features)
---------------------------------------------------------------------

GET> http://localhost:8080/api/2/things/:d908aca7-a29e-4387-a782-b162daa16ebc/features


-----------------------------------------------------------------------------------------
Create or modify all features of a specific thing at once:(PUT/things/{thingId}/features)
-----------------------------------------------------------------------------------------
PUT> http://localhost:8080/api/2/things/:d908aca7-a29e-4387-a782-b162daa16ebc/features


JSON:
 {
  "coffee-brewer": {
    "properties": {
      "definition": [
        "com.acme:coffeebrewer:0.1.0"
      ],
      "brewed-coffees": 0
    }
  },
  "water-tank": {
    "properties": {
      "configuration": {
        "smartMode": true,
        "brewingTemp": 87,
        "tempToHold": 44,
        "timeoutSeconds": 6000
      },
      "status": {
        "waterAmount": 731,
        "temperature": 44
      }
    }
  }
}


-------------------------------------------------------------------------
Patch all features of a specific thing:(PATCH/things/{thingId}/features):
-------------------------------------------------------------------------
PATCH> http://localhost:8080/api/2/things/:d908aca7-a29e-4387-a782-b162daa16ebc/features

The following example will add/update the properties brewed-coffees, tempToHold and failState. The configuration property smartMode will be deleted from the thing.

JSON: 
 {
  "coffee-brewer": {
    "properties": {
      "brewed-coffees": 10
    }
  },
  "water-tank": {
    "properties": {
      "configuration": {
        "smartMode": null,
        "tempToHold": 50
      },
      "status": {
        "failState": true
      }
    }
  }
}


----------------------------------------
Delete all features of a specific thing:(DELETE/things/{thingId}/features)
----------------------------------------
DELETE> http://localhost:8080/api/2/things/:d908aca7-a29e-4387-a782-b162daa16ebc/features



------------------------------------------------
Retrieve a specific feature of a specific thing:(GET/things/{thingId}/features/{featureId})
------------------------------------------------
GET> http://localhost:8080/api/2/things/:d908aca7-a29e-4387-a782-b162daa16ebc/features/coffee-brewer



--------------------------------------------------------
Create or modify a specific feature of a specific thing:(PUT/things/{thingId}/features/{featureId})
--------------------------------------------------------
PUT> http://localhost:8080/api/2/things/:d908aca7-a29e-4387-a782-b162daa16ebc/features/coffee-brewer

JSON:
 {
  "definition": [
    "com.acme:coffeemaker:0.1.0",
    "com.acme:coffeemaker:1.1.0"
  ],
  "properties": {
    "connected": true,
    "brewed-coffees": 0
  }
}



---------------------------------------------
Patch a specific feature of a specific thing:(PATCH/things/{thingId}/features/{featureId})
---------------------------------------------
PATCH> http://localhost:8080/api/2/things/:d908aca7-a29e-4387-a782-b162daa16ebc/features/coffee-brewer


JSON:
 {
  "definition": null,
  "properties": {
    "connected": true,
    "brewed-coffees": 0
  }
}



----------------------------------------------
Delete a specific feature of a specific thing:(DELETE/things/{thingId}/features/{featureId})
----------------------------------------------
DELETE> http://localhost:8080/api/2/things/:d908aca7-a29e-4387-a782-b162daa16ebc/features/coffee-brewer



---------------------------------
List the definition of a feature:(GET/things/{thingId}/features/{featureId}/definition)
---------------------------------
GET> http://localhost:8080/api/2/things/:d908aca7-a29e-4387-a782-b162daa16ebc/features/coffee-brewer/definition


---------------------------------------------
Create or update the definition of a feature:(PUT/things/{thingId}/features/{featureId}/definition)
---------------------------------------------
PUT> http://localhost:8080/api/2/things/:d908aca7-a29e-4387-a782-b162daa16ebc/features/coffee-brewer/definition


JSON:
 [
  "com.acme:coffeebrewer:0.1.0",
  "com.acme:coffeebrewer:1.0.0"
]



----------------------------------
Patch the definition of a feature:(PATCH/things/{thingId}/features/{featureId}/definition)
----------------------------------
PATCH> http://localhost:8080/api/2/things/:d908aca7-a29e-4387-a782-b162daa16ebc/features/coffee-brewer/definition

JSON:
 [
  "com.acme:coffeebrewer:0.1.0",
  "com.acme:coffeebrewer:1.1.0"
]



-----------------------------------
Delete the definition of a feature:(DELETE/things/{thingId}/features/{featureId}/definition)
-----------------------------------
DELETE> http://localhost:8080/api/2/things/:d908aca7-a29e-4387-a782-b162daa16ebc/features/coffee-brewer/definition


---------------------------------
List all properties of a feature:(GET/things/{thingId}/features/{featureId}/properties)
---------------------------------
GET> http://localhost:8080/api/2/things/:d908aca7-a29e-4387-a782-b162daa16ebc/features/coffee-brewer/properties


-----------------------------------------------------
Create or update all properties of a feature at once:(PUT/things/{thingId}/features/{featureId}/properties)
-----------------------------------------------------
PUT> http://localhost:8080/api/2/things/:d908aca7-a29e-4387-a782-b162daa16ebc/features/coffee-brewer/properties



----------------------------------
Patch all properties of a feature:(PATCH/things/{thingId}/features/{featureId}/properties)
----------------------------------
PATCH> http://localhost:8080/api/2/things/:d908aca7-a29e-4387-a782-b162daa16ebc/features/coffee-brewer/properties



-----------------------------------
Delete all properties of a feature:(DELETE/things/{thingId}/features/{featureId}/properties)
-----------------------------------
DELETE> http://localhost:8080/api/2/things/:d908aca7-a29e-4387-a782-b162daa16ebc/features/coffee-brewer/properties



------------------------------------------
Retrieve a specific property of a feature:(GET/things/{thingId}/features/{featureId}/properties/{propertyPath})
------------------------------------------
GET> http://localhost:8080/api/2/things/:d908aca7-a29e-4387-a782-b162daa16ebc/features/coffee-brewer/properties/configuration


--------------------------------------------------
Create or update a specific property of a feature:(PUT/things/{thingId}/features/{featureId}/properties/{propertyPath})
--------------------------------------------------
PUT> http://localhost:8080/api/2/things/:d908aca7-a29e-4387-a782-b162daa16ebc/features/coffee-brewer/properties/configuration



---------------------------------------
Patch a specific property of a feature:(PATCH/things/{thingId}/features/{featureId}/properties/{propertyPath})
---------------------------------------
PATCH> http://localhost:8080/api/2/things/:d908aca7-a29e-4387-a782-b162daa16ebc/features/coffee-brewer/properties/configuration



----------------------------------------
Delete a specific property of a feature:(DELETE/things/{thingId}/features/{featureId}/properties/{propertyPath})
----------------------------------------
DELETE> http://localhost:8080/api/2/things/:d908aca7-a29e-4387-a782-b162daa16ebc/features/coffee-brewer/properties/configuration


-----------------------------------------
List all desired properties of a feature:(GET/things/{thingId}/features/{featureId}/desiredProperties)
-----------------------------------------
GET> http://localhost:8080/api/2/things/:d908aca7-a29e-4387-a782-b162daa16ebc/features/coffee-brewer/desiredProperties


-------------------------------------------------------------
Create or update all desired properties of a feature at once:(PUT/things/{thingId}/features/{featureId}/desiredProperties)
-------------------------------------------------------------
PUT> http://localhost:8080/api/2/things/:d908aca7-a29e-4387-a782-b162daa16ebc/features/coffee-brewer/desiredProperties


JSON:
 {
  "configuration": {
    "smartMode": true,
    "brewingTemp": 87,
    "tempToHold": 44,
    "timeoutSeconds": 6000
  },
  "status": {
    "waterAmount": 731,
    "temperature": 44
  }
}



------------------------------------------
Patch all desired properties of a feature:(PATCH/things/{thingId}/features/{featureId}/desiredProperties)
------------------------------------------
PATCH> http://localhost:8080/api/2/things/:d908aca7-a29e-4387-a782-b162daa16ebc/features/coffee-brewer/desiredProperties


JSON:
 {
  "configuration": {
    "smartMode": null,
    "brewingTemp": 87,
    "tempToHold": 44,
    "timeoutSeconds": 6000
  },
  "status": {
    "waterAmount": 731,
    "temperature": 44
  }
}



-------------------------------------------
Delete all desired properties of a feature:(DELETE/things/{thingId}/features/{featureId}/desiredProperties)
-------------------------------------------
DELETE> http://localhost:8080/api/2/things/:d908aca7-a29e-4387-a782-b162daa16ebc/features/coffee-brewer/desiredProperties



--------------------------------------------------
Retrieve a specific desired property of a feature:(GET/things/{thingId}/features/{featureId}/desiredProperties/{propertyPath})
--------------------------------------------------
GET> http://localhost:8080/api/2/things/:d908aca7-a29e-4387-a782-b162daa16ebc/features/coffee-brewer/desiredProperties/status



----------------------------------------------------------
Create or update a specific desired property of a feature:(PUT/things/{thingId}/features/{featureId}/desiredProperties/{propertyPath})
----------------------------------------------------------
PUT> http://localhost:8080/api/2/things/:d908aca7-a29e-4387-a782-b162daa16ebc/features/coffee-brewer/desiredProperties/configuration/brewingTemp


JSON: 89

-----------------------------------------------
Patch a specific desired property of a feature:(PATCH/things/{thingId}/features/{featureId}/desiredProperties/{propertyPath})
-----------------------------------------------
PATCH> http://localhost:8080/api/2/things/:d908aca7-a29e-4387-a782-b162daa16ebc/features/coffee-brewer/desiredProperties/configuration/brewingTemp

JSON: 89


------------------------------------------------
Delete a specific desired property of a feature:(DELETE/things/{thingId}/features/{featureId}/desiredProperties/{propertyPath})
------------------------------------------------
DELETE> PATCH> http://localhost:8080/api/2/things/:d908aca7-a29e-4387-a782-b162daa16ebc/features/coffee-brewer/desiredProperties/configuration/brewingTemp




Policies(Control access to your things):
--

Retrieve a specific policy:(GET/policies/policyId)
---------------------------
GET> http://localhost:8080/api/2/policies/:d908aca7-a29e-4387-a782-b162daa16ebc


----------------------------------------------
Create or update a policy with a specified ID:(PUT/policies/policyId)
----------------------------------------------
PUT> http://localhost:8080/api/2/policies/:d908aca7-a29e-4387-a782-b162daa16ebc


-------------------------
Delete a specific policy:(DELETE/policies/policyId)
-------------------------
DELETE> http://localhost:8080/api/2/policies/:d908aca7-a29e-4387-a782-b162daa16ebc


---------------------------------------------------------
Activate subjects for this policy derived from the token:(POST/policies/{policyId}/actions/activateTokenIntegration
---------------------------------------------------------
POST> http://localhost:8080/api/2/policies/:d908aca7-a29e-4387-a782-b162daa16ebc/actions/activateTokenIntegration


JSON:
 {
  "announcement": {
    "beforeExpiry": "5m",
    "whenDeleted": true,
    "requestedAcks": {
      "labels": [
        "my-connection-id:my-issued-acknowledgement"
      ],
      "timeout": "30s"
    },
    "randomizationInterval": "5m"
  }
}


-----------------------------------------------------------
Deactivate subjects for this policy derived from the token:(POST/policies/{policyId}/actions/deactivateTokenIntegration
-----------------------------------------------------------
POST> http://localhost:8080/api/2/policies/:d908aca7-a29e-4387-a782-b162daa16ebc/actions/deactivateTokenIntegration


------------------------------------------
Retrieve the entries of a specific policy:(GET/policies/{policyId}/entries)
------------------------------------------
GET> http://localhost:8080/api/2/policies/:d908aca7-a29e-4387-a782-b162daa16ebc/entries


----------------------------------------
Modify the entries of a specific policy:(PUT/policies/{policyId}/entries)
----------------------------------------
PUT> http://localhost:8080/api/2/policies/:d908aca7-a29e-4387-a782-b162daa16ebc/entries


--------------------------------------------------------------
Retrieve the entries of a specific Label of a specific policy:(GET/policies/{policyId}/entries/{label})
--------------------------------------------------------------
GET> http://localhost:8080/api/2/policies/:d908aca7-a29e-4387-a782-b162daa16ebc/entries/DEFAULT


----------------------------------------------------------------------
Create or modify the entries of a specific Label of a specific policy:(PUT/policies/{policyId}/entries/{label})
----------------------------------------------------------------------
PUT> http://localhost:8080/api/2/policies/:d908aca7-a29e-4387-a782-b162daa16ebc/entries/DEFAULT


------------------------------------------------------------
Delete the entries of a specific Label of a specific policy:(DELETE/policies/{policyId}/entries/{label})
------------------------------------------------------------
DELETE> http://localhost:8080/api/2/policies/:d908aca7-a29e-4387-a782-b162daa16ebc/entries/DEFAULT


----------------------------------------------------------------
Activate a subject for this policy entry derived from the token:(POST//policies/{policyId}/entries/{label}/actions/activateTokenIntegration)
----------------------------------------------------------------
POST> http://localhost:8080/api/2/policies/:d908aca7-a29e-4387-a782-b162daa16ebc/entries/DEFAULT/actions/activateTokenIntegration


------------------------------------------------------------------
Deactivate a subject for this policy entry derived from the token:(POST//policies/{policyId}/entries/{label}/actions/deactivateTokenIntegration)
------------------------------------------------------------------
POST> http://localhost:8080/api/2/policies/:d908aca7-a29e-4387-a782-b162daa16ebc/entries/DEFAULT/actions/deactivateTokenIntegration


----------------------------------------------------------------
Retrieve all Subjects for a specific Label of a specific policy:(GET/policies/{policyId}/entries/{label}/subjects)
----------------------------------------------------------------
GET> http://localhost:8080/api/2/policies/:d908aca7-a29e-4387-a782-b162daa16ebc/entries/DEFAULT/subjects


------------------------------------------------------------------------
Create or modify all Subjects for a specific Label of a specific policy:(PUT/policies/{policyId}/entries/{label}/subjects)
------------------------------------------------------------------------
PUT> http://localhost:8080/api/2/policies/:d908aca7-a29e-4387-a782-b162daa16ebc/entries/DEFAULT/subjects


------------------------------------------------------------------------
Retrieve one specific Subject for a specific Label of a specific policy:(GET//policies/{policyId}/entries/{label}/subjects/{subjectId})
------------------------------------------------------------------------
GET> http://localhost:8080/api/2/policies/:00574f62-fd0d-462a-8982-98ce6ab252ad/entries/DEFAULT/subjects/nginx:ditto



--------------------------------------------------------------------------------
Create or modify one specific Subject for a specific Label of a specific policy:(PUT//policies/{policyId}/entries/{label}/subjects/{subjectId})
--------------------------------------------------------------------------------
PUT> http://localhost:8080/api/2/policies/:00574f62-fd0d-462a-8982-98ce6ab252ad/entries/DEFAULT/subjects/nginx:ditto


----------------------------------------------------------------------
Delete one specific Subject for a specific Label of a specific policy:(DELETE/policies/{policyId}/entries/{label}/subjects/{subjectId})
----------------------------------------------------------------------
DELETE> http://localhost:8080/api/2/policies/:00574f62-fd0d-462a-8982-98ce6ab252ad/entries/DEFAULT/subjects/nginx:ditto


------------------------------------------------------------------
Retrieve all Resources for a specific Label of a specific policy:(GET/policies/{policyId}/entries/{label}/resources)
------------------------------------------------------------------
GET> http://localhost:8080/api/2/policies/:00574f62-fd0d-462a-8982-98ce6ab252ad/entries/DEFAULT/resources


--------------------------------------------------------------------------
Create or modify all Resources for a specific Label of a specific policy:(PUT/policies/{policyId}/entries/{label}/resources)
--------------------------------------------------------------------------
PUT> http://localhost:8080/api/2/policies/:00574f62-fd0d-462a-8982-98ce6ab252ad/entries/DEFAULT/resources


-------------------------------------------------------------------------
Retrieve one specific Resource for a specific Label of a specific policy:(GET/policies/{policyId}/entries/{label}/resources/{resourcePath})
-------------------------------------------------------------------------
GET> http://localhost:8080/api/2/policies/:00574f62-fd0d-462a-8982-98ce6ab252ad/entries/DEFAULT/resources/policy:/


---------------------------------------------------------------------------------
Create or modify one specific Resource for a specific Label of a specific policy:(PUT/policies/{policyId}/entries/{label}/resources/{resourcePath})
---------------------------------------------------------------------------------
PUT> http://localhost:8080/api/2/policies/:00574f62-fd0d-462a-8982-98ce6ab252ad/entries/DEFAULT/resources/thing:/


-----------------------------------------------------------------------
Delete one specific Resource for a specific Label of a specific policy:(DELETE/policies/{policyId}/entries/{label}/resources/{resourcePath})
-----------------------------------------------------------------------
DELETE> http://localhost:8080/api/2/policies/:00574f62-fd0d-462a-8982-98ce6ab252ad/entries/DEFAULT/resources/message:/


----------------------------------------------
Retrieve information about the current caller:(GET/whoami)
----------------------------------------------
GET> http://localhost:8080/api/2/whoami




Things-Search (Find every thing):
--

Search for things(GET/search/things):
-------------------------------------
GET> http://localhost:8080/api/2/search/things


--------------------------------------
Count things(GET/search/things/count):
--------------------------------------
GET> http://localhost:8080/api/2/search/things/count



Messages(Talk with your things):
--

Initiates claiming a specific thing in order to gain access(POST/things/{thingId}/inbox/claim):
------------------------------------------------------------
The default (if omitted) requested acks is "requested-acks=live-response" which will block the HTTP call until a subscriber of the live message sends a response.

Curl:
----
curl -X 'POST' \
  'https://ditto.eclipseprojects.io/api/2/things/com.ditto/inbox/claim?timeout=60&requested-acks=requested-acks%3Dlive-response' \
  -H 'accept: */*' \
  -H 'Authorization: Basic ZGl0dG86ZGl0dG8=' \
  -H 'Content-Type: application/json' \
  -d '""'
  
Request URL:
------------
https://ditto.eclipseprojects.io/api/2/things/com.ditto/inbox/claim?timeout=60&requested-acks=requested-acks%3Dlive-response


------------------------------------------
CloudEvents(Process CloudEvents in Ditto):
------------------------------------------
CloudEvents defines how events are mapped to HTTP 1.1 request and response messages.

Processes a CloudEvent sent in Ditto protocol
---------------------------------------------
POST> http://localhost:8080/api/2/cloudevents


JSON:
 {
  "topic": "org.eclipse.ditto/thing-1/things/twin/commands/modify",
  "path": "/",
  "value": {
    "attributes": {
      "foo": 42
    }
  }
}


--------------------------------
Connections(Manage Connections):
--------------------------------
 The default devops credentials are username: devops, password: foobar. The password can be changed by setting the environment variable DEVOPS_PASSWORD in the gateway service.

->click on Authorization, select Basic Auth. In Basic Auth provide username as devops and password as foobar.

------------------------------------------
Create a new connection(POST/connections):
------------------------------------------
POST>http://localhost:8080/api/2/connections

JSON:
 {
  "name": "hono-example-connection-123",
  "connectionType": "amqp-10",
  "connectionStatus": "open",
  "uri": "amqps://user:password@hono.eclipseprojects.io:5671",
  "sources": [
    {
      "addresses": [
        "telemetry/FOO",
        "..."
      ],
      "authorizationContext": [
        "ditto:inbound-auth-subject",
        "..."
      ],
      "consumerCount": 1,
      "enforcement": {
        "input": "{{ header:device_id }}",
        "filters": [
          "{{ thing:id }}"
        ]
      },
      "payloadMapping": [
        "Ditto"
      ]
    }
  ],
  "targets": [
    {
      "address": "events/twin",
      "topics": [
        "_/_/things/twin/events"
      ],
      "authorizationContext": [
        "ditto:outbound-auth-subject",
        "..."
      ],
      "headerMapping": {
        "message-id": "{{ header:correlation-id }}",
        "content-type": "application/vnd.eclipse.ditto+json"
      }
    }
  ]
}


Output(Json Response):
----------------------

{
    "id": "a0317674-bca9-4eff-b242-1935d0820e72",
    "name": "hono-example-connection-123",
    "connectionType": "amqp-10",
    "connectionStatus": "open",
    "uri": "amqps://user:password@hono.eclipseprojects.io:5671",
    "sources": [
        {
            "addresses": [
                "telemetry/FOO",
                "..."
            ],
            "consumerCount": 1,
            "authorizationContext": [
                "ditto:inbound-auth-subject",
                "..."
            ],
            "enforcement": {
                "input": "{{ header:device_id }}",
                "filters": [
                    "{{ thing:id }}"
                ]
            },
            "headerMapping": {
                "content-type": "{{header:content-type}}",
                "reply-to": "{{header:reply-to}}",
                "correlation-id": "{{header:correlation-id}}"
            },
            "payloadMapping": [
                "Ditto"
            ],
            "replyTarget": {
                "address": "{{header:reply-to}}",
                "headerMapping": {
                    "content-type": "{{header:content-type}}",
                    "correlation-id": "{{header:correlation-id}}"
                },
                "expectedResponseTypes": [
                    "response",
                    "error"
                ],
                "enabled": true
            }
        }
    ],
    "targets": [
        {
            "address": "events/twin",
            "topics": [
                "_/_/things/twin/events"
            ],
            "authorizationContext": [
                "ditto:outbound-auth-subject",
                "..."
            ],
            "headerMapping": {
                "message-id": "{{ header:correlation-id }}",
                "content-type": "application/vnd.eclipse.ditto+json"
            }
        }
    ],
    "clientCount": 1,
    "failoverEnabled": true,
    "validateCertificates": true,
    "processorPoolSize": 1,
    "tags": []
}


------------------------------------------
Retrieve all connections(GET/connections):
------------------------------------------
GET> http://localhost:8080/api/2/connections


---------------------------------------------------------------
Retrieve a specific connection(GET/connections/{connectionId}):
---------------------------------------------------------------
GET> http://localhost:8080/api/2/connections/a0317674-bca9-4eff-b242-1935d0820e72


------------------------------------------------------------------------
Update a specific connection registered(PUT/connections/{connectionId}):
------------------------------------------------------------------------
PUT> http://localhost:8080/api/2/connections/a0317674-bca9-4eff-b242-1935d0820e72

JSON: added "status"  in the payloadMapping


----------------------------------------------------------------
Delete a specific connection(DELETE/connections/{connectionId}):
-----------------------------------------------------------------
DELETE> http://localhost:8080/api/2/connections/a0317674-bca9-4eff-b242-1935d0820e72


---------------------------------------------------------------------------------
Send a command to a specific connection(POST/connections/{connectionId}/command):
---------------------------------------------------------------------------------
POST> http://localhost:8080/api/2/connections/c137c0c3-3be1-44f2-9252-c154538f4873

JSON: connectivity.commands:openConnection
 

--------------------------------------------------------------------------------
Retrieve status of a specific connection(GET/connections/{connectionId}/status):
--------------------------------------------------------------------------------
GET> http://localhost:8080/api/2/connections/a0317674-bca9-4eff-b242-1935d0820e72/status


---------------------------------------------------------------------------------
Retrieve metrics of a specific connection(GET/connections/{connectionId}/metrics):
---------------------------------------------------------------------------------
GET> http://localhost:8080/api/2/connections/a0317674-bca9-4eff-b242-1935d0820e72/metrics


--------------------------------------------------------------------------------
Retrieve logs of a specific connection(GET/connections/{connectionId}/metrics):
--------------------------------------------------------------------------------
GET> http://localhost:8080/api/2/connections/a0317674-bca9-4eff-b242-1935d0820e72/logs

