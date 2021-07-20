```powershell PowerShell (REST API)
function Get-OctopusItems
{
	# Define parameters
    param(
    	$OctopusUri,
        $ApiKey,
        $SkipCount = 0
    )
    
    # Define working variables
    $items = @()
    $skipQueryString = ""
    $headers = @{"X-Octopus-ApiKey"="$ApiKey"}

    # Check to see if there there is already a querystring
    if ($octopusUri.Contains("?"))
    {
        $skipQueryString = "&skip="
    }
    else
    {
        $skipQueryString = "?skip="
    }

    $skipQueryString += $SkipCount
    
    # Get intial set
    $resultSet = Invoke-RestMethod -Uri "$($OctopusUri)$skipQueryString" -Method GET -Headers $headers

    # Check to see if it returned an item collection
    if ($resultSet.Items)
    {
        # Store call results
        $items += $resultSet.Items
    
        # Check to see if resultset is bigger than page amount
        if (($resultSet.Items.Count -gt 0) -and ($resultSet.Items.Count -eq $resultSet.ItemsPerPage))
        {
            # Increment skip count
            $SkipCount += $resultSet.ItemsPerPage

            # Recurse
            $items += Get-OctopusItems -OctopusUri $OctopusUri -ApiKey $ApiKey -SkipCount $SkipCount
        }
    }
    else
    {
        return $resultSet
    }
    

    # Return results
    return $items
}

$apikey = 'API-YourAPIKey' # Get this from your profile
$OctopusUrl = 'https://YourURL' # Your Octopus Server address
$spaceName = "Default"

# Create headers for API calls
$headers = @{"X-Octopus-ApiKey"="$ApiKey"}

$lifecycleName = "MyLifecycle"

# Get space
$space = (Get-OctopusItems -OctopusUri "$octopusURL/api/spaces" -ApiKey $ApiKey) | Where-Object {$_.Name -eq $spaceName}

# Get lifecycles
$lifecycles = Get-OctopusItems -OctopusUri "$octopusURL/api/$($space.Id)/lifecycles" -ApiKey $apikey

# Check to see if lifecycle already exists
if ($null -eq ($lifecycles | Where-Object {$_.Name -eq $lifecycleName}))
{
    # Create payload
    $jsonPayload = @{
        Id = $null
        Name = $lifecycleName
        SpaceId = $space.Id
        Phases = @()
        ReleaseRetentionPolicy = @{
            ShouldKeepForever = $true
            QuantityToKeep = 0
            Unit = "Days"
        }
        TentacleRetnetionPolicy = @{
            ShouldKeepForever = $true
            QuantityToKeep = 0
            Unit = "Days"
        }
        Links = $null
    }

    # Create new lifecycle
    Invoke-RestMethod -Method Post -Uri "$OctopusUrl/api/$($space.Id)/lifecycles" -Body ($jsonPayload | ConvertTo-Json -Depth 10) -Headers $headers
}
else
{
    Write-Host "$lifecycleName already exists."
}
```
```powershell PowerShell (Octopus.Client)
# Load assembly
Add-Type -Path 'path:\to\Octopus.Client.dll'
$octopusURL = "https://YourURL"
$octopusAPIKey = "API-YourAPIKey"
$spaceName = "Default"
$lifecycleName = "MyLifecycle"

$endpoint = New-Object Octopus.Client.OctopusServerEndpoint($octopusURL, $octopusAPIKey)
$repository = New-Object Octopus.Client.OctopusRepository($endpoint)
$client = New-Object Octopus.Client.OctopusClient($endpoint)

# Get space
$space = $repository.Spaces.FindByName($spaceName)
$repositoryForSpace = $client.ForSpace($space)

# Check to see if lifecycle already exists
if ($null -eq $repositoryForSpace.Lifecycles.FindByName($lifecycleName))
{
    # Create new lifecyle
    $lifecycle = New-Object Octopus.Client.Model.LifecycleResource
    $lifecycle.Name = $lifecycleName
    $repositoryForSpace.Lifecycles.Create($lifecycle)
}
else
{
    Write-Host "$lifecycleName already exists."
}
```
```csharp C#
#r "path\to\Octopus.Client.dll"
using Octopus.Client;
using Octopus.Client.Model;
using System;
using System.Linq;
// If using .net Core, be sure to add the NuGet package of System.Security.Permissions

var octopusURL = "https://YourURL";
var octopusAPIKey = "API-YourAPIKey";
var spaceName = "Default";
var lifecycleName = "MyLifecycle";

// Create repository object
var endpoint = new OctopusServerEndpoint(octopusURL, octopusAPIKey);
var repository = new OctopusRepository(endpoint);
var client = new OctopusClient(endpoint);

// Get space
var space = repository.Spaces.FindByName(spaceName);
var spaceRepository = client.ForSpace(space);

if (null == spaceRepository.Lifecycles.FindByName(lifecycleName))
{
	// Create new lifecycle
	var lifecycle = new Octopus.Client.Model.LifecycleResource();
	lifecycle.Name = lifecycleName;
	spaceRepository.Lifecycles.Create(lifecycle);
}
else
{
	Console.Write(string.Format("{0} already exists.", lifecycleName));
}
```
```python Python3
import json
import requests
from requests.api import get, head

def get_octopus_resource(uri, headers, skip_count = 0):
    items = []
    skip_querystring = ""

    if '?' in uri:
        skip_querystring = '&skip='
    else:
        skip_querystring = '?skip='

    response = requests.get((uri + skip_querystring + str(skip_count)), headers=headers)
    response.raise_for_status()

    # Get results of API call
    results = json.loads(response.content.decode('utf-8'))

    # Store results
    if 'Items' in results.keys():
        items += results['Items']

        # Check to see if there are more results
        if (len(results['Items']) > 0) and (len(results['Items']) == results['ItemsPerPage']):
            skip_count += results['ItemsPerPage']
            items += get_octopus_resource(uri, headers, skip_count)

    else:
        return results

    
    # return results
    return items

# Define Octopus server variables
octopus_server_uri = 'https://shawnsesna.octopusdemos.app'
octopus_api_key = 'API-LSXOSYSOZ92B752AIIFVTFFZHA'
headers = {'X-Octopus-ApiKey': octopus_api_key}
space_name = "Default"
lifecycle_name = "MyLifecycle"

# Get space
uri = '{0}/api/spaces'.format(octopus_server_uri)
spaces = get_octopus_resource(uri, headers)
space = next((x for x in spaces if x['Name'] == space_name), None)

# Get lifecycles
uri = '{0}/api/{1}/lifecycles'.format(octopus_server_uri, space['Id'])
lifecycles = get_octopus_resource(uri, headers)
lifecycle = next((x for x in lifecycles if x['Name'] == lifecycle_name), None)

# Check to see if lifecycle already exists
if None == lifecycle:
    # Create new lifecycle
    lifecycle = {
        'Id': None,
        'Name': lifecycle_name,
        'SpaceId': space['Id'],
        'Phases': [],
        'ReleaseRetentionPolicy': {
            'ShouldKeepForever': True,
            'QuantityToKeep': 0,
            'Unit': 'Days'
        },
        'TentacleRetentionPolicy': {
            'ShouldKeepForever': True,
            'QuantityToKeep': 0,
            'Unit': 'Days'
        },
        'Links': None
    }

    response = requests.post(uri, headers=headers, json=lifecycle)
    response.raise_for_status()
else:
    print ('{0} already exists.'.format(lifecycle_name))
```
```go Go
package main

import (
	"fmt"
	"log"

	"net/url"

	"github.com/OctopusDeploy/go-octopusdeploy/octopusdeploy"
)

func main() {

	apiURL, err := url.Parse("https://YourURL")
	if err != nil {
		log.Println(err)
	}
	APIKey := "API-YourAPIKey"
	spaceName := "MySpace"
	lifecycleName := "MyLifecycle"

	// Get reference to space
	space := GetSpace(apiURL, APIKey, spaceName)

	// Check to see if the lifecycle already exists
	if GetLifecycle(apiURL, APIKey, space, lifecycleName) == nil {
		lifecycle := CreateLifecycle(apiURL, APIKey, space, lifecycleName)
		fmt.Println(lifecycle.Name + " created successfully")
	} else {
		fmt.Println(lifecycleName + " already exists.")
	}
}

func octopusAuth(octopusURL *url.URL, APIKey, space string) *octopusdeploy.Client {
	client, err := octopusdeploy.NewClient(nil, octopusURL, APIKey, space)
	if err != nil {
		log.Println(err)
	}

	return client
}

func GetSpace(octopusURL *url.URL, APIKey string, spaceName string) *octopusdeploy.Space {
	client := octopusAuth(octopusURL, APIKey, "")

	// Get specific space object
	space, err := client.Spaces.GetByName(spaceName)

	if err != nil {
		log.Println(err)
	} else {
		fmt.Println("Retrieved space " + space.Name)
	}

	return space
}

func GetLifecycle(octopusURL *url.URL, APIKey string, space *octopusdeploy.Space, LifecycleName string) *octopusdeploy.Lifecycle {
	client := octopusAuth(octopusURL, APIKey, space.ID)

	lifecycles, err := client.Lifecycles.GetByPartialName(LifecycleName)

	if err != nil {
		log.Println(err)
	}

	for i := 0; i < len(lifecycles); i++ {
		if lifecycles[i].Name == LifecycleName {
			return lifecycles[i]
		}
	}

	return nil
}

func CreateLifecycle(octopusURL *url.URL, APIKey string, space *octopusdeploy.Space, name string) *octopusdeploy.Lifecycle {
	client := octopusAuth(octopusURL, APIKey, space.ID)
	lifecycle := octopusdeploy.NewLifecycle(name)

	client.Lifecycles.Add(lifecycle)

	return lifecycle
}
```