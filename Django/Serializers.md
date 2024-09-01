Serializers allow complex data such as *querysets* and *model instances* to be converted to native *Python datatypes* that can then be rendered into `JSON`, `XML` or other content types.
Also, they provide **deserialization**, allowing parsed data to be converted back into complex types, after first validating the incoming data.

The serializers work similarly to `Form` and `ModelForm` in Django. They provide a `Serializer` class which gives the control of responses, and `ModelSerializer` class which provides shortcuts serializers that deal with model instances and querysets.

#### Declaring Serializers

Simple object for example purposes:
```
from datetime improt datetime

class Comment:
	def __init__(self, email, content, created=None):
		self.email = email
		self.content = content
		self.created = created or datetime.now()

comment = Comment(email='akhio@example.com', content='foo bar')
```

We'll declare a serializer to serialize and deserialize data that corresponds  to `Comment`  objects.
Declaring a serializer:
```
from rest_framework import serializers

class CommentSerializer(serializers.Serializer):
	email = serializers.EmailField()
	content = serializers.CharField(max_length=200)
	created = serializers.DateTimeField()
```

#### Serializing objects
Now we serialize data:
```
serializer = CommentSerializer(comment)
serializer.data
# {'email':'akhio@example.com', 'content':'foo bar', 'created:some_date'}
```

Now, as we translated the model instance into Python native datatypes, to finalize the serialization we render the data into `json`:
```
from rest_framework.renderers import JSONRenderer

json = JSONRenderer().render(serializer.data)
json
# b'{'email':'akhio@example.com', 'content':'foo bar', 'created:some_date'}'
```

#### Deserializing objects

First we parse a stream into Python native datatypes:
```
import io
from rest_framework.parsers import JSONParser

stream = io.BytesIO(json)
data = JSONParser().parse(stream)
```
and then restore these native datatypes into a dictionary of validated data:
```
serializer = CommentSerializer(data=data)
serializer.is_valid()
# True
serializer.validated_data
# {'email':'akhio@example.com', 'content':'foo bar', 'created:some_date'}
```
