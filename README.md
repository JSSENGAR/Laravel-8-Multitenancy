# Laravel-8-Multitenancy

Laravel 8 Multitenancy

1. composer create-project laravel/laravel projectName
2. cd projectName
3. composer require "spatie/laravel-multitenancy:^1.0"
4. php artisan vendor:publish --provider="Spatie\Multitenancy\MultitenancyServiceProvider" --tag="config"
5. php artisan vendor:publish --provider="Spatie\Multitenancy\MultitenancyServiceProvider" --tag="migrations"
6. edit app/Http/Kernel.php
		
		To prevent users from a tenant abusing their session to access another tenant, you must use the Spatie\Multitenancy\Http\Middleware\EnsureValidTenantSession middleware on all tenant-aware routes.
		If all your application routes are tenant-aware, you can add it to your global middleware in app\Http\Kernel.php
		// in `app\Http\Kernel.php`
		protected $middlewareGroups = [
			'web' => [
				// ...
				\Spatie\Multitenancy\Http\Middleware\NeedsTenant::class,
				\Spatie\Multitenancy\Http\Middleware\EnsureValidTenantSession::class,
			]
		];
		If only some routes are tenant-aware, create a new middleware group:
		// in `app\Http\Kernel.php`
		protected $middlewareGroups = [
		// ...
		'tenant' => [
        \Spatie\Multitenancy\Http\Middleware\NeedsTenant::class,
        \Spatie\Multitenancy\Http\Middleware\EnsureValidTenantSession::class,
		]
		];

7. 
  7.1 config/multitenancy.php --> 'landlord_database_connection_name' => 'landlord',
  7.2   
		'switch_tenant_tasks' => [
			// add tasks here
			Spatie\Multitenancy\Tasks\SwitchTenantDatabaseTask::class,
	    ],
  7.3 'tenant_finder' => Spatie\Multitenancy\TenantFinder\DomainTenantFinder::class, 		
    
8. env --> 
	
	DB_CONNECTION=tenant
	DB_HOST=127.0.0.1
	DB_PORT=3306
	DB_DATABASE=tenancy
	DB_USERNAME=root
	DB_PASSWORD=

9. config/database.php --> 

replace	: -->

	'mysql' => [
            'driver' => 'mysql',
            'url' => env('DATABASE_URL'),
            'host' => env('DB_HOST', '127.0.0.1'),
            'port' => env('DB_PORT', '3306'),
            'database' => env('DB_DATABASE', 'forge'),
            'username' => env('DB_USERNAME', 'forge'),
            'password' => env('DB_PASSWORD', ''),
            'unix_socket' => env('DB_SOCKET', ''),
            'charset' => 'utf8mb4',
            'collation' => 'utf8mb4_unicode_ci',
            'prefix' => '',
            'prefix_indexes' => true,
            'strict' => true,
            'engine' => null,
            'options' => extension_loaded('pdo_mysql') ? array_filter([
                PDO::MYSQL_ATTR_SSL_CA => env('MYSQL_ATTR_SSL_CA'),
            ]) : [],
        ],		
	with
	   'tenant' => [
            'driver' => 'mysql',
            'url' => env('DATABASE_URL'),
            'host' => env('DB_HOST', '127.0.0.1'),
            'port' => env('DB_PORT', '3306'),
            'database' => null,
            'username' => env('DB_USERNAME', 'forge'),
            'password' => env('DB_PASSWORD', ''),
            'unix_socket' => env('DB_SOCKET', ''),
            'charset' => 'utf8mb4',
            'collation' => 'utf8mb4_unicode_ci',
            'prefix' => '',
            'prefix_indexes' => true,
            'strict' => true,
            'engine' => null,
            'options' => extension_loaded('pdo_mysql') ? array_filter([
                PDO::MYSQL_ATTR_SSL_CA => env('MYSQL_ATTR_SSL_CA'),
            ]) : [],
        ],



        'landlord' => [
            'driver' => 'mysql',
            'url' => env('DATABASE_URL'),
            'host' => env('DB_HOST', '127.0.0.1'),
            'port' => env('DB_PORT', '3306'),
            'database' => env('DB_DATABASE', 'forge'),
            'username' => env('DB_USERNAME', 'forge'),
            'password' => env('DB_PASSWORD', ''),
            'unix_socket' => env('DB_SOCKET', ''),
            'charset' => 'utf8mb4',
            'collation' => 'utf8mb4_unicode_ci',
            'prefix' => '',
            'prefix_indexes' => true,
            'strict' => true,
            'engine' => null,
            'options' => extension_loaded('pdo_mysql') ? array_filter([
                PDO::MYSQL_ATTR_SSL_CA => env('MYSQL_ATTR_SSL_CA'),
            ]) : [],
        ],
10. php artisan vendor:publish --provider="Spatie\Multitenancy\MultitenancyServiceProvider" --tag="migrations"
11. php artisan migrate --path=database/migrations/landlord --database=landlord
12.
		-- Database: `tenancy`	
		-- Table structure for table `migrations`		

		DROP TABLE IF EXISTS `migrations`;
		CREATE TABLE IF NOT EXISTS `migrations` (
		`id` int UNSIGNED NOT NULL AUTO_INCREMENT,
		`migration` varchar(255) COLLATE utf8mb4_unicode_ci NOT NULL,
		`batch` int NOT NULL,
		PRIMARY KEY (`id`)
		) ENGINE=MyISAM DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
		--
		-- Table structure for table `tenants`
		--
		DROP TABLE IF EXISTS `tenants`;
		CREATE TABLE IF NOT EXISTS `tenants` (
		`id` int NOT NULL,
		`name` varchar(250) NOT NULL,
		`domain` varchar(250) NOT NULL,
		`database` varchar(250) NOT NULL,
		`created_at` timestamp NOT NULL,
		`updated_at` timestamp NOT NULL
		) ENGINE=MyISAM DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci;

		--
		-- Dumping data for table `tenants`
		--

		INSERT INTO `tenants` (`id`, `name`, `domain`, `database`, `created_at`, `updated_at`) VALUES
		(1, 'shop', 'shop.localhost', 'shop', '0000-00-00 00:00:00', '0000-00-00 00:00:00'),
		(2, 'admin', 'admin.localhost', 'admin', '0000-00-00 00:00:00', '0000-00-00 00:00:00');
		(3, 'sjv', 'sjv.localhost', 'sjv', '0000-00-00 00:00:00', '0000-00-00 00:00:00');
		(4, 'stt', 'stt.localhost', 'admin', '0000-00-00 00:00:00', '0000-00-00 00:00:00');
		COMMIT;

13. php artisan vendor:publish --provider="Spatie\Multitenancy\MultitenancyServiceProvider" --tag="migrations"
14. php artisan migrate --path=database/migrations/landlord --database=landlord
15. php artisan tenants:artisan "migrate --database=tenant"
16. php artisan tenants:artisan "migrate --path=database/migrations/tenant --database=tenant" 
17. resourses/view/welcome.blade.php  
		@if(\Spatie\Multitenancy\Models\Tenant::checkCurrent())
			<h1 style="color:black"> {{app('currentTenant')->name}}</h1>
		@endif
18. php artisan serve
