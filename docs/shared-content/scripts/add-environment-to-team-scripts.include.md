```powershell PowerShell (REST API)
$ErrorActionPreference = "Stop";

# Define working variables
$octopusURL = "https://youroctourl"
$octopusAPIKey = "API-YOURAPIKEY"
$header = @{ "X-Octopus-ApiKey" = $octopusAPIKey }
$spaceName = "default"
$teamName = "MyTeam"
$userRoleName = "Deployment creator"
$environmentNames = @("Development", "Staging")
$environmentIds = @()

# Get space
$space = (Invoke-RestMethod -Method Get -Uri "$octopusURL/api/spaces/all" -Headers $header) | Where-Object {$_.Name -eq $spaceName}

# Get team
$team = (Invoke-RestMethod -Method Get -Uri "$octopusURL/api/$($space.Id)/teams/all" -Headers $header) | Where-Object {$_.Name -eq $teamName}

# Get user role
$userRole = (Invoke-RestMethod -Method Get -Uri "$octopusURL/api/userroles/all" -Headers $header) | Where-Object {$_.Name -eq $userRoleName}

# Get scoped user role reference
$scopedUserRole = (Invoke-RestMethod -Method Get -Uri "$octopusURL/api/$($space.Id)/teams/$($team.Id)/scopeduserroles" -Headers $header).Items | Where-Object {$_.UserRoleId -eq $userRole.Id}

# Get Environments
$environments = (Invoke-RestMethod -Method Get -Uri "$octopusURL/api/$($space.Id)/environments/all" -Headers $header) | Where-Object {$environmentNames -contains $_.Name}
foreach ($environment in $environments)
{
    $environmentIds += $environment.Id
}

# Update the scopedUserRole
$scopedUserRole.EnvironmentIds += $environmentIds

Invoke-RestMethod -Method Put -Uri "$octopusURL/api/scopeduserroles/$($scopedUserRole.Id)" -Headers $header -Body ($scopedUserRole | ConvertTo-Json -Depth 10)
```
```powershell PowerShell (Octopus.Client)
# Load octopus.client assembly
Add-Type -Path "path\to\Octopus.Client.dll"

# Octopus variables
$octopusURL = "https://youroctourl"
$octopusAPIKey = "API-YOURAPIKEY"
$spaceName = "default"
$environmentNames = @("Test", "Production")
$teamName = "MyTeam"
$userRoleName = "Deployment creator"

$endpoint = New-Object Octopus.Client.OctopusServerEndpoint $octopusURL, $octopusAPIKey
$repository = New-Object Octopus.Client.OctopusRepository $endpoint
$client = New-Object Octopus.Client.OctopusClient $endpoint
$environmentIds = @()

try
{
    # Get space
    $space = $repository.Spaces.FindByName($spaceName)
    $repositoryForSpace = $client.ForSpace($space)

    # Get team
    $team = $repositoryForSpace.Teams.FindByName($teamName)

    # Get user role
    $userRole = $repositoryForSpace.UserRoles.FindByName($userRoleName)
    
    # Get scopeduserrole
    $scopedUserRole = $repositoryForSpace.Teams.GetScopedUserRoles($team) | Where-Object {$_.UserRoleId -eq $userRole.Id}

    # Get environments
    $environments = $repositoryForSpace.Environments.GetAll() | Where-Object {$environmentNames -contains $_.Name}
    foreach ($environment in $environments)
    {
        # Add Id
        $scopedUserRole.EnvironmentIds.Add($environment.Id)
    }

    # Update the scoped user role object
    $repositoryForSpace.ScopedUserRoles.Modify($scopedUserRole)
}
catch
{
    Write-Host $_.Exception.Message
}
```
```csharp C#
// If using .net Core, be sure to add the NuGet package of System.Security.Permissions
#r "path\to\Octopus.Client.dll"

using Octopus.Client;
using Octopus.Client.Model;

// Declare working varibles
var octopusURL = "https://youroctourl";
var octopusAPIKey = "API-YOURAPIKEY";
string spaceName = "default";
string[] environmentNames = { "Development", "Production" };
string teamName = "MyTeam";
string userRoleName = "Deployment creator";

// Create repository object
var endpoint = new OctopusServerEndpoint(octopusURL, octopusAPIKey);
var repository = new OctopusRepository(endpoint);
var client = new OctopusClient(endpoint);

try
{
    // Get space
    var space = repository.Spaces.FindByName(spaceName);
    var repositoryForSpace = client.ForSpace(space);

    // Get team
    var team = repositoryForSpace.Teams.FindByName(teamName);

    // Get user role
    var userRole = repository.UserRoles.FindByName(userRoleName);

    // Get scoped user role
    var scopedUserRole = repository.Teams.GetScopedUserRoles(team).FirstOrDefault(s => s.UserRoleId == userRole.Id);

    // Get environment ids
    foreach (var environmentName in environmentNames)
    {
        scopedUserRole.EnvironmentIds.Add(repositoryForSpace.Environments.FindByName(environmentName).Id);
    }

    // Update scoped user role
    repositoryForSpace.ScopedUserRoles.Modify(scopedUserRole);
}
catch (Exception ex)
{
    Console.WriteLine(ex.Message);
    return;
}
```
```python Python3
import json
import requests

# Define Octopus server variables
octopus_server_uri = 'https://YourUrl'
octopus_api_key = 'API-YourAPIKey'
headers = {'X-Octopus-ApiKey': octopus_api_key}
space_name = 'Default'
team_name = 'MyTeam'
user_role_name = 'MyRole'
environment_names = ['List', 'of', 'environment names']

uri = '{0}/spaces/all'.format(octopus_server_uri)
response = requests.get(uri, headers=headers)
response.raise_for_status()

# Get space
spaces = json.loads(response.content.decode('utf-8'))
space = next((x for x in spaces if x['Name'] == space_name), None)

# Get team
uri = '{0}/{1}/teams'.format(octopus_server_uri, space['Id'])
response = requests.get(uri, headers=headers)
response.raise_for_status
teams = json.loads(response.content.decode('utf-8'))
team = next((x for x in teams['Items'] if x['Name'] == team_name), None)

# Get userrole
uri = '{0}/userroles'.format(octopus_server_uri)
response = requests.get(uri, headers=headers)
response.raise_for_status
userroles = json.loads(response.content.decode('utf-8'))
userrole = next((x for x in userroles['Items'] if x['Name'] == user_role_name), None)

# Get scopeduserrole
uri = '{0}/{1}/teams/{2}/scopeduserroles'.format(octopus_server_uri, space['Id'], team['Id'])
response = requests.get(uri, headers=headers)
response.raise_for_status
scopeduserroles = json.loads(response.content.decode('utf-8'))
scopeduserrole = next((x for x in scopeduserroles['Items'] if x['UserRoleId'] == userrole['Id']), None)

# Get environments
uri = '{0}/{1}/environments'.format(octopus_server_uri, space['Id'])
response = requests.get(uri, headers=headers)
response.raise_for_status
environments = json.loads(response.content.decode('utf-8'))

# Loop through environment names
for environment_name in environment_names:
    environment = next((x for x in environments['Items'] if x['Name'] == environment_name), None)
    scopeduserrole['EnvironmentIds'].append(environment['Id'])

# Update the user role
uri = '{0}/{1}/scopeduserroles/{2}'.format(octopus_server_uri, space['Id'], scopeduserrole['Id'])
response = requests.put(uri, headers=headers, json=scopeduserrole)
response.raise_for_status
```