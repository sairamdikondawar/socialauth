# Introduction #

This is a step by step guide to show how to write your own custom plugin for a provider in SocialAuth.


## Step 1. Create an interface for desired plugin ##

First you will have to create an interface for the desired plugin. For example if you want to create a Feed Plugin for any provider then first of all you will have to create an interface and it must extends  org.brickred.socialauth.Plugin interface. e.g

```
Public interface FeedPlugin extends Plugin{
	Public List getFeeds();
}
```
**NOTE:** - If you are creating a plugin which is already created for other provider then you can use existing interface for that.


## Step 2. Write a Plugin implementation ##

Second step is to write a Plugin implementation which implements the interface created in Step1. Following functionality must be available in this implementation:-
  1. This implementation must have parameterized constructor of type org.brickred.socialauth.util.ProviderSupport.
  1. This implementation must provide the definition of Plugin interface methods. These methods are getter/setter for org.brickred.socialauth.util.ProviderSupport property.
**NOTE:** - ProviderSupport class provides a method to make OAuth specific HTTP call.

```
public class FeedPluginImpl implements FeedPlugin, Serializable {

	private ProviderSupport providerSupport;

	public FeedPluginImpl(final ProviderSupport providerSupport) {
		this.providerSupport = providerSupport;
	}

	@Override
	public List<Feed> getFeeds() throws Exception {
		List<Feed> list = new ArrayList<Feed>();
                //Code to get Feeds 
		return list;
	}

	@Override
	public ProviderSupport getProviderSupport() {
		return providerSupport;
	}

	@Override
	public void setProviderSupport(final ProviderSupport providerSupport) {
		this.providerSupport = providerSupport;

	}
}

```

## Step 3. Register custom plugin ##
You can register custom plugin by making entry in oauth\_consumer.properties file. Suppose you are creating a plugin for Facebook then you will have to make following entry for registering your plugin:-
```
graph.facebook.com.consumer_key =152190004803645
graph.facebook.com.consumer_secret =64c94bd02180b0ade85889b44b2ba7c4
graph.facebook.com.plugins = org.brickred.socialauth.plugin.facebook.AlbumPluginImpl
```

## Step 4. Configure required scope/permission for plugin ##
If there is a need to pass special scope for plugin functionality then you can configured this by doing following entry on oauth\_consumer.properties file.
```
graph.facebook.com.consumer_key =152190004803645
graph.facebook.com.consumer_secret =64c94bd02180b0ade85889b44b2ba7c4
graph.facebook.com.plugins = org.brickred.socialauth.plugin.facebook.AlbumPluginImpl
org.brickred.socialauth.plugin.facebook.AlbumPluginImpl.scope=user_photos
```

## Step 5. Use plugin ##
Now you can get plugin from provider object and use the functionality of plugin. You have to write the following code to get the plugin from provider object:-
```
if (provider.isSupportedPlugin(org.brickred.socialauth.plugin.AlbumsPlugin.class)) {
    AlbumsPlugin p = provider.getPlugin(org.brickred.socialauth.plugin.AlbumsPlugin.class);
    List<Album> albums = p.getAlbums();
}
```

Here first we are checking, does provider support the required plugin or not? We have to just use the interface name for checking and getting the required plugin from provider instead of passing concrete provider class name.