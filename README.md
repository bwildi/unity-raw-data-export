# unityrawdataexport
unityrawdataexport is a Python 3 package for pulling raw data from the Unity Analytics REST API. See the [Unity documentation](https://docs.unity3d.com/Manual/UnityAnalyticsRawDataExport.html) for more information on how this works.

## Installation

## Quickstart guide
Firstly, create a UnityDataImporter object and include your Unity project id and your API key:

```python
from unityrawdataexport import *

upid = "aa43ae0a-a7a7-4016-ae96-e253bb126aa8"
key = "166291ff148b2878375a8e54aebb1549"
udi = UnityDataImporter(upid, key)
```

To pull data from the API you need to first either create a new data export, or use a data export that has been created previously. For the latter case, you need to get the export_id. The list_data_exports method can be used to identify previously created data exports:

```python
# Get a json of the metadata of all data exports
exports = udi.list_data_exports()

# get the id of the most recent export
export_id = exports[0]['id']
```

You can then use the get_data_export method to get a json of the data from this export:

```python
data = udi.get_data_export(export_id)
```

If you need to create a new export, you can run create_export. Make sure when setting the parameters that you include a startDate, endDate and dataset. For help on parameters, see the [Unity documentation](https://docs.unity3d.com/Manual/UnityAnalyticsRawDataExport.html).

```python
params = {
  'startDate': '2019-09-01',
  'endDate': '2019-09-30',
  'dataset': 'appStart'
}

udi.create_export(params)
```

This will create a new export_id, which is associated with the class through the export_id attribute. The export will usually take a minute or two to be created. You can check its status by looking at the export metadata:

```python
print(udi.get_data_export(output='metadata')['status'])
```
Note that it was not necessary to specify the export_id this time. Once the status is completed, you can get a json of the data:

```python
data = udi.get_data_export()
```

Alternatively, you can do all this in one step using the create_and_get_export method:

```python
data = udi.create_and_get_export(params)
```

## pandas and longer exports
For convenience, there are a couple of methods in the package that allow you to output pandas dataframes. Firstly, any json data file you produce from the previous step can be converted using the convert_to_pandas function

```python
df = convert_to_pandas(data)
```

Secondly, the make_long_df method can be used to export more than 31 days of data at once. It does this by creating multiple exports and appending them together into a single pandas DataFrame.

```python
params['startDate'] = '2019-08-01'
params['endDate'] = '2019-10-14'
df = udi.make_long_df(params)
```

## Maintenance

I made this package in a professional context. I am no longer able to test the functionality of the package, because I do not have access to a Unity Analytics account. I will fix obvious errors where I can, but if the Unity API changes in such a way that breaks this package, I will not be able to repair it.
