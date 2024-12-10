# LaraRickAndMorty

# Rick and Morty Characters Viewer

Este proyecto es una aplicación Java que muestra los personajes de "Rick and Morty" utilizando la API pública. La interfaz gráfica está construida con Swing y permite navegar entre las páginas de personajes, mostrando detalles e imágenes de cada uno.

## Características

- Mostrar una tabla con los personajes de "Rick and Morty".
- Navegación entre páginas: primera, anterior, siguiente y última.
- Deshabilitar botones de navegación cuando no hay más páginas disponibles.
- Mostrar imágenes y detalles de cada personaje.

## Antes que nada. Estructura del Proyecto Java

La estructura del proyecto Java es la siguiente:

```
.editorconfig
pom.xml
src/
  main/
    java/
      com/
        maximo/
          ApiResponse.java
          Character.java
          CharacterTableModel.java
          Info.java
          Location.java
          Main.java
          Origin.java
          RickAndMortyFrame.java
          RickAndMortyService.java
  test/
    java/
      com/
        maximo/
          AppTest.java
target/
  classes/
    com/
      maximo/
        ApiResponse.class
        Character.class
        CharacterTableModel.class
        Info.class
        Location.class
        Main.class
        Origin.class
        RickAndMortyFrame.class
        RickAndMortyService.class
  test-classes/
    com/
      maximo/
        AppTest.class
```

## Paso 1: Crear un Proyecto Laravel

Primero, crea un nuevo proyecto Laravel:

```sh
composer create-project --prefer-dist laravel/laravel RickAndMortyLaravel
cd RickAndMortyLaravel
```

## Paso 2: Crear Modelos y Migraciones

### Modelo y Migración para Character



Crea el modelo y la migración para Character:


```sh
php artisan make:model Character -m
```

En el archivo de migración `database/migrations/xxxx_xx_xx_create_characters_table.php`, define la estructura de la tabla:

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class CreateCharactersTable extends Migration
{
    public function up()
    {
        Schema::create('characters', function (Blueprint $table) {
            $table->id();
            $table->string('name');
            $table->string('status');
            $table->string('species');
            $table->string('type')->nullable();
            $table->string('gender');
            $table->unsignedBigInteger('origin_id')->nullable();
            $table->unsignedBigInteger('location_id')->nullable();
            $table->string('image');
            $table->json('episode');
            $table->string('url');
            $table->timestamp('created_at')->nullable();
            $table->timestamps();
        });
    }

    public function down()
    {
        Schema::dropIfExists('characters');
    }
}
```

### Modelo y Migración para  Origin



Crea el modelo y la migración para Origin:

```sh
php artisan make:model Origin -m
```

En el archivo de migración `database/migrations/xxxx_xx_xx_create_origins_table.php`, define la estructura de la tabla:

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class CreateOriginsTable extends Migration
{
    public function up()
    {
        Schema::create('origins', function (Blueprint $table) {
            $table->id();
            $table->string('name');
            $table->string('url');
            $table->timestamps();
        });
    }

    public function down()
    {
        Schema::dropIfExists('origins');
    }
}
```

### Modelo y Migración para Location

Crea el modelo y la migración para Location:

```sh
php artisan make:model Location -m
```

En el archivo de migración `database/migrations/xxxx_xx_xx_create_locations_table.php`, define la estructura de la tabla:

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class CreateLocationsTable extends Migration
{
    public function up()
    {
        Schema::create('locations', function (Blueprint $table) {
            $table->id();
            $table->string('name');
            $table->string('url');
            $table->timestamps();
        });
    }

    public function down()
    {
        Schema::dropIfExists('locations');
    }
}
```

### Modelo y Migración para Info



Crea el modelo y la migración para Info:

```sh
php artisan make:model Info -m
```

En el archivo de migración `database/migrations/xxxx_xx_xx_create_infos_table.php`, define la estructura de la tabla:

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class CreateInfosTable extends Migration
{
    public function up()
    {
        Schema::create('infos', function (Blueprint $table) {
            $table->id();
            $table->integer('count');
            $table->integer('pages');
            $table->string('next')->nullable();
            $table->string('prev')->nullable();
            $table->timestamps();
        });
    }

    public function down()
    {
        Schema::dropIfExists('infos');
    }
}
```

### Modelo y Migración para ApiResponse

Crea el modelo y la migración para ApiResponse

:

```sh
php artisan make:model ApiResponse -m
```

En el archivo de migración `database/migrations/xxxx_xx_xx_create_api_responses_table.php`, define la estructura de la tabla:

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class CreateApiResponsesTable extends Migration
{
    public function up()
    {
        Schema::create('api_responses', function (Blueprint $table) {
            $table->id();
            $table->unsignedBigInteger('info_id');
            $table->timestamps();

            $table->foreign('info_id')->references('id')->on('infos');
        });
    }

    public function down()
    {
        Schema::dropIfExists('api_responses');
    }
}
```

## Paso 3: Definir Relaciones en los Modelos

### Modelo 

Character



```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class Character extends Model
{
    protected $fillable = [
        'name', 'status', 'species', 'type', 'gender', 'origin_id', 'location_id', 'image', 'episode', 'url', 'created_at'
    ];

    protected $casts = [
        'episode' => 'array',
    ];

    public function origin()
    {
        return $this->belongsTo(Origin::class);
    }

    public function location()
    {
        return $this->belongsTo(Location::class);
    }
}
```

### Modelo 

Origin



```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class Origin extends Model
{
    protected $fillable = ['name', 'url'];

    public function characters()
    {
        return $this->hasMany(Character::class);
    }
}
```

### Modelo 

Location



```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class Location extends Model
{
    protected $fillable = ['name', 'url'];

    public function characters()
    {
        return $this->hasMany(Character::class);
    }
}
```

### Modelo 

Info



```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class Info extends Model
{
    protected $fillable = ['count', 'pages', 'next', 'prev'];

    public function apiResponses()
    {
        return $this->hasMany(ApiResponse::class);
    }
}
```

### Modelo 

ApiResponse



```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class ApiResponse extends Model
{
    protected $fillable = ['info_id'];

    public function info()
    {
        return $this->belongsTo(Info::class);
    }

    public function characters()
    {
        return $this->hasMany(Character::class);
    }
}
```

## Paso 4: Crear Controladores y Vistas

### Controlador para 

Character



Crea el controlador:

```sh
php artisan make:controller CharacterController
```

En el archivo app/Http/Controllers/CharacterController.php:

```php
<?php

namespace App\Http\Controllers;

use App\Models\Character;

class CharacterController extends Controller
{
    public function index()
    {
        $characters = Character::all();
        return view('characters.index', compact('characters'));
    }
}
```

### Vista para Character



Crea la vista resources/views/characters/index.blade.php:

```blade
@extends('layouts.app')

@section('content')
<table>
    <thead>
        <tr>
            <th>ID</th>
            <th>Nombre</th>
            <th>Estado</th>
            <th>Especie</th>
            <th>Imagen</th>
        </tr>
    </thead>
    <tbody>
        @foreach($characters as $character)
            <tr>
                <td>{{ $character->id }}</td>
                <td>{{ $character->name }}</td>
                <td>{{ $character->status }}</td>
                <td>{{ $character->species }}</td>
                <td>
                    <img src="{{ $character->image }}" alt="{{ $character->name }}" width="50" height="50">
                </td>
            </tr>
        @endforeach
    </tbody>
</table>
@endsection
```

### Rutas

Añade la ruta en routes/web.php:

```php
use App\Http\Controllers\CharacterController;

Route::get('/characters', [CharacterController::class, 'index']);
```

### Controlador para Info



Crea el controlador:

```sh
php artisan make:controller InfoController
```

En el archivo app/Http/Controllers/InfoController.php:

```php
<?php

namespace App\Http\Controllers;

use App\Models\Info;

class InfoController extends Controller
{
    public function show($id)
    {
        $info = Info::findOrFail($id);
        return view('info', compact('info'));
    }
}
```

### Vista para Info



Crea la vista resources/views/info.blade.php:

```blade
@extends('layouts.app')

@section('content')
    <h1>Información</h1>
    <p>Conteo: {{ $info->count }}</p>
    <p>Páginas: {{ $info->pages }}</p>
    <p>Siguiente: {{ $info->next }}</p>
    <p>Anterior: {{ $info->prev }}</p>
@endsection
```

### Rutas

Añade la ruta en routes/web.php:

```php
use App\Http\Controllers\InfoController;

Route::get('/info/{id}', [InfoController::class, 'show']);
```

## Paso 5: Ejecutar Migraciones

Ejecuta las migraciones para crear las tablas en la base de datos:

```sh
php artisan migrate
```

## Paso 6: Crear Servicio para Consumir la API

Crea un servicio para consumir la API de Rick and Morty en app/Services/RickAndMortyService.php:

```php
<?php

namespace App\Services;

use Illuminate\Support\Facades\Http;
use App\Models\ApiResponse;

class RickAndMortyService
{
    private const BASE_URL = 'https://rickandmortyapi.com/api/character/';

    public function getCharacters(int $page): ApiResponse
    {
        $response = Http::get(self::BASE_URL, ['page' => $page]);

        if ($response->failed()) {
            throw new \Exception('Error: ' . $response->status());
        }

        return ApiResponse::create($response->json());
    }
}
```

## Paso 7: Integrar el Servicio en el Controlador

Modifica el controlador `CharacterController` para usar el servicio:

```php
<?php

namespace App\Http\Controllers;
# Aplicación en Laravel que visualiza los personajes de Rick & Morty.

## Estructura del Proyecto Java

La estructura del proyecto Java es la siguiente:

```
.editorconfig
pom.xml
src/
  main/
    java/
      com/
        maximo/
          ApiResponse.java
          Character.java
          CharacterTableModel.java
          Info.java
          Location.java
          Main.java
          Origin.java
          RickAndMortyFrame.java
          RickAndMortyService.java
  test/
    java/
      com/
        maximo/
          AppTest.java
target/
  classes/
    com/
      maximo/
        ApiResponse.class
        Character.class
        CharacterTableModel.class
        Info.class
        Location.class
        Main.class
        Origin.class
        RickAndMortyFrame.class
        RickAndMortyService.class
  test-classes/
    com/
      maximo/
        AppTest.class
```

## Paso 1: Crear un Proyecto Laravel

Primero, crea un nuevo proyecto Laravel:

```sh
composer create-project --prefer-dist laravel/laravel RickAndMortyLaravel
cd RickAndMortyLaravel
```

## Paso 2: Crear Modelos y Migraciones

### Modelo y Migración para Character



Crea el modelo y la migración para Character:


```sh
php artisan make:model Character -m
```

En el archivo de migración `database/migrations/xxxx_xx_xx_create_characters_table.php`, define la estructura de la tabla:

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class CreateCharactersTable extends Migration
{
    public function up()
    {
        Schema::create('characters', function (Blueprint $table) {
            $table->id();
            $table->string('name');
            $table->string('status');
            $table->string('species');
            $table->string('type')->nullable();
            $table->string('gender');
            $table->unsignedBigInteger('origin_id')->nullable();
            $table->unsignedBigInteger('location_id')->nullable();
            $table->string('image');
            $table->json('episode');
            $table->string('url');
            $table->timestamp('created_at')->nullable();
            $table->timestamps();
        });
    }

    public function down()
    {
        Schema::dropIfExists('characters');
    }
}
```

### Modelo y Migración para  Origin



Crea el modelo y la migración para Origin:

```sh
php artisan make:model Origin -m
```

En el archivo de migración `database/migrations/xxxx_xx_xx_create_origins_table.php`, define la estructura de la tabla:

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class CreateOriginsTable extends Migration
{
    public function up()
    {
        Schema::create('origins', function (Blueprint $table) {
            $table->id();
            $table->string('name');
            $table->string('url');
            $table->timestamps();
        });
    }

    public function down()
    {
        Schema::dropIfExists('origins');
    }
}
```

### Modelo y Migración para Location

Crea el modelo y la migración para Location:

```sh
php artisan make:model Location -m
```

En el archivo de migración `database/migrations/xxxx_xx_xx_create_locations_table.php`, define la estructura de la tabla:

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class CreateLocationsTable extends Migration
{
    public function up()
    {
        Schema::create('locations', function (Blueprint $table) {
            $table->id();
            $table->string('name');
            $table->string('url');
            $table->timestamps();
        });
    }

    public function down()
    {
        Schema::dropIfExists('locations');
    }
}
```

### Modelo y Migración para Info



Crea el modelo y la migración para Info:

```sh
php artisan make:model Info -m
```

En el archivo de migración `database/migrations/xxxx_xx_xx_create_infos_table.php`, define la estructura de la tabla:

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class CreateInfosTable extends Migration
{
    public function up()
    {
        Schema::create('infos', function (Blueprint $table) {
            $table->id();
            $table->integer('count');
            $table->integer('pages');
            $table->string('next')->nullable();
            $table->string('prev')->nullable();
            $table->timestamps();
        });
    }

    public function down()
    {
        Schema::dropIfExists('infos');
    }
}
```

### Modelo y Migración para ApiResponse

Crea el modelo y la migración para ApiResponse

:

```sh
php artisan make:model ApiResponse -m
```

En el archivo de migración `database/migrations/xxxx_xx_xx_create_api_responses_table.php`, define la estructura de la tabla:

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class CreateApiResponsesTable extends Migration
{
    public function up()
    {
        Schema::create('api_responses', function (Blueprint $table) {
            $table->id();
            $table->unsignedBigInteger('info_id');
            $table->timestamps();

            $table->foreign('info_id')->references('id')->on('infos');
        });
    }

    public function down()
    {
        Schema::dropIfExists('api_responses');
    }
}
```

## Paso 3: Definir Relaciones en los Modelos

### Modelo 

Character



```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class Character extends Model
{
    protected $fillable = [
        'name', 'status', 'species', 'type', 'gender', 'origin_id', 'location_id', 'image', 'episode', 'url', 'created_at'
    ];

    protected $casts = [
        'episode' => 'array',
    ];

    public function origin()
    {
        return $this->belongsTo(Origin::class);
    }

    public function location()
    {
        return $this->belongsTo(Location::class);
    }
}
```

### Modelo 

Origin



```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class Origin extends Model
{
    protected $fillable = ['name', 'url'];

    public function characters()
    {
        return $this->hasMany(Character::class);
    }
}
```

### Modelo 

Location



```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class Location extends Model
{
    protected $fillable = ['name', 'url'];

    public function characters()
    {
        return $this->hasMany(Character::class);
    }
}
```

### Modelo 

Info



```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class Info extends Model
{
    protected $fillable = ['count', 'pages', 'next', 'prev'];

    public function apiResponses()
    {
        return $this->hasMany(ApiResponse::class);
    }
}
```

### Modelo 

ApiResponse



```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class ApiResponse extends Model
{
    protected $fillable = ['info_id'];

    public function info()
    {
        return $this->belongsTo(Info::class);
    }

    public function characters()
    {
        return $this->hasMany(Character::class);
    }
}
```

## Paso 4: Crear Controladores y Vistas

### Controlador para 

Character



Crea el controlador:

```sh
php artisan make:controller CharacterController
```

En el archivo app/Http/Controllers/CharacterController.php:

```php
<?php

namespace App\Http\Controllers;

use App\Models\Character;

class CharacterController extends Controller
{
    public function index()
    {
        $characters = Character::all();
        return view('characters.index', compact('characters'));
    }
}
```

### Vista para Character



Crea la vista resources/views/characters/index.blade.php:

```blade
@extends('layouts.app')

@section('content')
<table>
    <thead>
        <tr>
            <th>ID</th>
            <th>Nombre</th>
            <th>Estado</th>
            <th>Especie</th>
            <th>Imagen</th>
        </tr>
    </thead>
    <tbody>
        @foreach($characters as $character)
            <tr>
                <td>{{ $character->id }}</td>
                <td>{{ $character->name }}</td>
                <td>{{ $character->status }}</td>
                <td>{{ $character->species }}</td>
                <td>
                    <img src="{{ $character->image }}" alt="{{ $character->name }}" width="50" height="50">
                </td>
            </tr>
        @endforeach
    </tbody>
</table>
@endsection
```

### Rutas

Añade la ruta en routes/web.php:

```php
use App\Http\Controllers\CharacterController;

Route::get('/characters', [CharacterController::class, 'index']);
```

### Controlador para Info



Crea el controlador:

```sh
php artisan make:controller InfoController
```

En el archivo app/Http/Controllers/InfoController.php:

```php
<?php

namespace App\Http\Controllers;

use App\Models\Info;

class InfoController extends Controller
{
    public function show($id)
    {
        $info = Info::findOrFail($id);
        return view('info', compact('info'));
    }
}
```

### Vista para Info



Crea la vista resources/views/info.blade.php:

```blade
@extends('layouts.app')

@section('content')
    <h1>Información</h1>
    <p>Conteo: {{ $info->count }}</p>
    <p>Páginas: {{ $info->pages }}</p>
    <p>Siguiente: {{ $info->next }}</p>
    <p>Anterior: {{ $info->prev }}</p>
@endsection
```

### Rutas

Añade la ruta en routes/web.php:

```php
use App\Http\Controllers\InfoController;

Route::get('/info/{id}', [InfoController::class, 'show']);
```

## Paso 5: Ejecutar Migraciones

Ejecuta las migraciones para crear las tablas en la base de datos:

```sh
php artisan migrate
```

## Paso 6: Crear Servicio para Consumir la API

Crea un servicio para consumir la API de Rick and Morty en app/Services/RickAndMortyService.php:

```php
<?php

namespace App\Services;

use Illuminate\Support\Facades\Http;
use App\Models\ApiResponse;

class RickAndMortyService
{
    private const BASE_URL = 'https://rickandmortyapi.com/api/character/';

    public function getCharacters(int $page): ApiResponse
    {
        $response = Http::get(self::BASE_URL, ['page' => $page]);

        if ($response->failed()) {
            throw new \Exception('Error: ' . $response->status());
        }

        return ApiResponse::create($response->json());
    }
}
```

## Paso 7: Integrar el Servicio en el Controlador

Modifica el controlador `CharacterController` para usar el servicio:

```php
<?php

namespace App\Http\Controllers;

use App\Services\RickAndMortyService;

class CharacterController extends Controller
{
    protected $service;

    public function __construct(RickAndMortyService $service)
    {
        $this->service = $service;
    }

    public function index()
    {
        $apiResponse = $this->service->getCharacters(1);
        $characters = $apiResponse->characters;
        return view('characters.index', compact('characters'));
    }
}
```

## Paso 8: Ejecutar la Aplicación

Finalmente, ejecuta la aplicación Laravel:

```sh
php artisan serve
```

Visita `http://localhost:8000/characters` para ver la lista de personajes.
use App\Services\RickAndMortyService;

class CharacterController extends Controller
{
    protected $service;

    public function __construct(RickAndMortyService $service)
    {
        $this->service = $service;
    }

    public function index()
    {
        $apiResponse = $this->service->getCharacters(1);
        $characters = $apiResponse->characters;
        return view('characters.index', compact('characters'));
    }
}
```

## Paso 8: Ejecutar la Aplicación

Finalmente, ejecuta la aplicación Laravel:

```sh
php artisan serve
```

Visita `http://localhost:8000/characters` para ver la lista de personajes.
