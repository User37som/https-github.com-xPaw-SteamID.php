# SteamID.php [![Build Status](https://img.shields.io/travis/xPaw/SteamID.php.svg)](https://travis-ci.org/xPaw/SteamID.php) [![Test Coverage](https://api.codeclimate.com/v1/badges/0a9a150b0f0575b3c8a1/test_coverage)](https://codeclimate.com/github/xPaw/SteamID.php/test_coverage) [![Packagist](https://img.shields.io/packagist/dt/xpaw/steamid.svg)](https://packagist.org/packages/xpaw/steamid)

This 64bit structure is used for identifying various objects on the Steam 
network. This library provides an easy way to work with SteamIDs and makes 
conversions easy.

This library does not use subtraction hacks like described on 
[Valve Developer Wiki](https://developer.valvesoftware.com/wiki/SteamID), 
or used in many other functions.

SteamID.php requires PHP version to be 5.4 or higher, and [GMP module](http://php.net/manual/en/book.gmp.php)
to be installed to perform operations on unsigned 64-bit integers.

## Brief overview

A SteamID is made up of four parts: its **universe**, its **type**, its 
**instance**, and its **account ID**.

- **Universe**: Currently there are 5 universes. A universe is a unique 
  instance of Steam. You'll probably only be interacting with the public universe, 
  which is the regular Steam. Only Valve employees can access non-public universes.
- **Type**: A SteamID's type determines what it identifies. The most common type 
  is *individual*, for user accounts. There are also other types such as *clans* 
  (Steam groups), *gameservers*, and more.
- **Instance**: Steam allows three simultaneous user account instances right now 
  *(1 = desktop, 2 = console, 4 = web, 0 = all)*
- **Account ID**: This represents a unique account of a type.

## Using this library

It's really easy to use it, as constructor automatically figures out given input 
and works its magic from there. If provided SteamID is not in a valid format, an 
`InvalidArgumentException` is thrown. You can call `IsValid` on given SteamID 
instance to perform various checks which make sure that given account type / 
universe / instance are correct. You can view [test file](.test.php) for 
multiple examples on how to manipulate SteamIDs.

### Example

```php
try
{
	// Constructor also accepts Steam3 and Steam2 representations
	$s = new SteamID( '76561197984981409' );
}
catch( InvalidArgumentException $e )
{
	echo 'Given SteamID could not be parsed.';
}

// Renders SteamID in it's Steam3 representation (e.g. [U:1:24715681])
echo $s->RenderSteam3() . PHP_EOL;

// Renders SteamID in it's Steam2 representation (e.g. STEAM_0:1:12357840)
echo $s->RenderSteam2() . PHP_EOL;

// Converts this SteamID into it's 64bit integer form (e.g. 76561197984981409)
echo $s->ConvertToUInt64() . PHP_EOL;
```

### SteamID normalization

If you run some website where users can enter their own SteamIDs, sometimes you
 might encounter SteamIDs which have wrong universe or instanceid set, which 
 will result in a completely different, yet valid, SteamID. To avoid this, you
  can manipulate given SteamID and set universe to public and instance to 
  desktop.

```php
try
{
	$s = new SteamID( $ID );
	
	if( $s->GetAccountType() !== SteamID :: TypeIndividual )
	{
		throw new InvalidArgumentException( 'We only support individual SteamIDs.' );
	}
	else if( !$s->IsValid() )
	{
		throw new InvalidArgumentException( 'Invalid SteamID.' );
	}
	
	$s->SetAccountInstance( SteamID :: DesktopInstance );
	$s->SetAccountUniverse( SteamID :: UniversePublic );
}
catch( InvalidArgumentException $e )
{
	echo $e->getMessage();
}
```

After doing these steps, you can call `RenderSteam3`, `RenderSteam2` or 
`ConvertToUInt64` to get normalized SteamID.

## Functions

<table>
	<thead>
		<tr>
			<th>Name</th>
			<th>Parameters</th>
			<th>Description</th>
		</tr>
	</thead>
	<tbody>
		<tr>
			<td>IsValid</td>
			<td>-</td>
			<td>Gets a value indicating whether this instance is valid.</td>
		</tr>
		<tr>
			<td>RenderSteam2</td>
			<td>-</td>
			<td>Renders this instance into it's Steam2 "STEAM_" representation.</td>
		</tr>
		<tr>
			<td>RenderSteam3</td>
			<td>-</td>
			<td>Renders this instance into it's Steam3 representation.</td>
		</tr>
		<tr>
			<td>ConvertToUInt64</td>
			<td>-</td>
			<td>Converts this SteamID into it's 64bit integer form.</td>
		</tr>
		<tr>
			<td>SetFromUInt64</td>
			<td>string or int (e.g 765...)</td>
			<td>Sets the various components of this SteamID from a 64bit integer form.</td>
		</tr>
		<tr>
			<td>GetAccountID</td>
			<td>-</td>
			<td>Gets the account id.</td>
		</tr>
		<tr>
			<td>GetAccountInstance</td>
			<td>-</td>
			<td>Gets the account instance.</td>
		</tr>
		<tr>
			<td>GetAccountType</td>
			<td>-</td>
			<td>Gets the account type.</td>
		</tr>
		<tr>
			<td>GetAccountUniverse</td>
			<td>-</td>
			<td>Gets the account universe.</td>
		</tr>
		<tr>
			<td>SetAccountID</td>
			<td>New account id</td>
			<td>Sets the account id.</td>
		</tr>
		<tr>
			<td>SetAccountInstance</td>
			<td>New account instance</td>
			<td>Sets the account instance. (e.g. <code>SteamID::DesktopInstance</code>)</td>
		</tr>
		<tr>
			<td>SetAccountType</td>
			<td>New account type</td>
			<td>Sets the account type. (e.g. <code>SteamID::TypeAnonGameServer</code>)</td>
		</tr>
		<tr>
			<td>SetAccountUniverse</td>
			<td>New account universe</td>
			<td>Sets the account universe. (e.g. <code>SteamID::UniversePublic</code>)</td>
		</tr>
	</tbody>
</table>

## License

[MIT](LICENSE)
