# About the workspace
* While you can get objects form the workspace, it is not the preferred way to do it. We suggest using [DataFileUtils](https://github.com/bio-boris/NewKbaseDevelopers/blob/master/recipes/FileUtilsRecipes.md#datafileutil) instead.
However, if you don't want to retrieve the entire object, and just want information about it, the most efficient way is to use the `ws.get_object_info` function.
If you want to read more about the available functions, see the spec file and search through the repository at 
[Workspace Deluxe](https://github.com/kbase/workspace_deluxe)

## Initialize Workspace Client and get an object's information by reference
    from Workspace.WorkspaceClient import Workspace as Workspace
    self.ws_url = config["workspace-url"]
    self.token = config["KB_AUTH_TOKEN"]
    self.ws = Workspace(self.ws_url, token=self.token)
    obj_ref = "your/object/reference"
    object_info = self.ws.get_object_info([{"ref": obj_ref}])[0]
    object_type = object_info[2] #this could be KBaseGenomes.Genome-8.3

## How to use the Workspace Client in the narrative
    ws = biokbase.narrative.clients.get('workspace')
    obj = ws.get_objects2({'objects' : [{'ref' : '30170/2/1'}]})
See this [example narrative](https://narrative.kbase.us/narrative/ws.30170.obj.1)


## How to add types to the workspace
* See the guide at https://ci.kbase.us/services/ws/docs/administrationinterface.html#managing-module-ownership-requests
* Use the administration interface in any of the clients above to register, approve registration of, and to upload typespecs

## How to get a given typespec / .spec file?
* If you can't find a typespec in a repo, you can find it doing the following:
#### Example Spec File Snippet

    from biokbase.workspace.client import Workspace
    ws = Workspace('https://ci.kbase.us/services/ws')
    print ws.get_module_info({'mod': 'YourModuleName'})['spec']

