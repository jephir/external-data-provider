# ExternalDataProvider

ExternalDataProvider is a CLIK-compatible data provider that uses ExternalInterface to retrieve data items. This can be used in UDK to automatically update your CLIK components with data directly from the game. The only function you have to write is an UnrealScript function that returns an array of the requested data.

## Example: Listing team players

There are two ScrollingList instances on the stage called "blueTeamList" and "redTeamList".

__actions: Frame 1__

```
blueTeamList.dataProvider = new ExternalDataProvider("GetPlayerNames", "blue");
redTeamList.dataProvider = new ExternalDataProvider("GetPlayerNames", "red");
```

The constructor parameters starts with the UnrealScript function name that will be used to get the array of data. After are the parameters that will be passed to that function. Any number of parameters can be specified and they can be of any type usable in an ExternalInterface call.

In UnrealScript, create the corresponding function in the GFxMoviePlayer class that is controlling the movie.

__TeamListGFxMoviePlayer.uc__

```
function array<string> GetPlayerNames(string TeamName)
{
	local array<string> Data;
	local PlayerReplicationInfo PRI;
	local byte TeamIndex;

	TeamIndex = TeamName ~= "red" ? 0 : 1;

	foreach GetPC().WorldInfo.GRI.PRIArray(PRI)
		if (PRI.Team != None && PRI.Team.TeamIndex == TeamIndex)
			Data.AddItem(PRI.PlayerName);

	return Data;
}
```

The data provider is now setup to use the player list from the game.

To refresh the data provider when the player list has changed, call invalidate in ActionScript.

```
blueTeamList.dataProvider.invalidate();
redTeamList.dataProvider.invalidate();
```
