						HOW TO CREATE  AUTHENTICATION USING  ADMIN TABLE



1. Create admin model amd admin Migration

		php artisan make:model Admin  -m

2. Past this code in Admin model class in App/Admin.php



		<?php

			namespace App;

			use Illuminate\Contracts\Auth\MustVerifyEmail;
			use Illuminate\Foundation\Auth\User as Authenticatable;
			use Illuminate\Notifications\Notifiable;

			class Admin extends Authenticatable
			{
			    use Notifiable;

			    /**
			     * The attributes that are mass assignable.
			     *
			     * @var array
			     */
			    protected $fillable = [
			        'name', 'email', 'password',
			    ];

			    /**
			     * The attributes that should be hidden for arrays.
			     *
			     * @var array
			     */
			    protected $hidden = [
			        'password', 'remember_token',
			    ];

			    /**
			     * The attributes that should be cast to native types.
			     *
			     * @var array
			     */
			    protected $casts = [
			        'email_verified_at' => 'datetime',
			    ];
			}



3. Update User.php by this code in App/User.php


	<?php

		namespace App;

		use Illuminate\Database\Eloquent\Model;

		class User extends Model
		{
		    //
		}


4. Make some Changes in App\Http\Controllers\Controller\RegisterController.php



		<?php

		namespace App\Http\Controllers\Auth;

		use App\Http\Controllers\Controller;
		use App\Providers\RouteServiceProvider;
		use App\Admin;
		use Illuminate\Foundation\Auth\RegistersUsers;
		use Illuminate\Support\Facades\Hash;
		use Illuminate\Support\Facades\Validator;

		class RegisterController extends Controller
		{
		    /*
		    |--------------------------------------------------------------------------
		    | Register Controller
		    |--------------------------------------------------------------------------
		    |
		    | This controller handles the registration of new users as well as their
		    | validation and creation. By default this controller uses a trait to
		    | provide this functionality without requiring any additional code.
		    |
		    */

		    use RegistersUsers;

		    /**
		     * Where to redirect users after registration.
		     *
		     * @var string
		     */
		    protected $redirectTo = RouteServiceProvider::HOME;

		    /**
		     * Create a new controller instance.
		     *
		     * @return void
		     */
		    public function __construct()
		    {
		        $this->middleware('guest');
		    }

		    /**
		     * Get a validator for an incoming registration request.
		     *
		     * @param  array  $data
		     * @return \Illuminate\Contracts\Validation\Validator
		     */
		    protected function validator(array $data)
		    {
		        return Validator::make($data, [
		            'name' => ['required', 'string', 'max:255'],
		            'email' => ['required', 'string', 'email', 'max:255', 'unique:users'],
		            'password' => ['required', 'string', 'min:8', 'confirmed'],
		        ]);
		    }

		    /**
		     * Create a new user instance after a valid registration.
		     *
		     * @param  array  $data
		     * @return \App\User
		     */
		    protected function create(array $data)
		    {
		        return Admin::create([
		            'name' => $data['name'],
		            'email' => $data['email'],
		            'password' => Hash::make($data['password']),
		        ]);
		    }
		}



5. make Change in Config/auth.php


				<?php

		return [

		    /*
		    |--------------------------------------------------------------------------
		    | Authentication Defaults
		    |--------------------------------------------------------------------------
		    |
		    | This option controls the default authentication "guard" and password
		    | reset options for your application. You may change these defaults
		    | as required, but they're a perfect start for most applications.
		    |
		    */

		    'defaults' => [
		        'guard' => 'web',
		        'passwords' => 'admins',
		    ],

		    /*
		    |--------------------------------------------------------------------------
		    | Authentication Guards
		    |--------------------------------------------------------------------------
		    |
		    | Next, you may define every authentication guard for your application.
		    | Of course, a great default configuration has been defined for you
		    | here which uses session storage and the Eloquent user provider.
		    |
		    | All authentication drivers have a user provider. This defines how the
		    | users are actually retrieved out of your database or other storage
		    | mechanisms used by this application to persist your user's data.
		    |
		    | Supported: "session", "token"
		    |
		    */

		    'guards' => [
		        'web' => [
		            'driver' => 'session',
		            'provider' => 'admins',
		        ],

		        'api' => [
		            'driver' => 'token',
		            'provider' => 'admins',
		            'hash' => false,
		        ],
		    ],

		    /*
		    |--------------------------------------------------------------------------
		    | User Providers
		    |--------------------------------------------------------------------------
		    |
		    | All authentication drivers have a user provider. This defines how the
		    | users are actually retrieved out of your database or other storage
		    | mechanisms used by this application to persist your user's data.
		    |
		    | If you have multiple user tables or models you may configure multiple
		    | sources which represent each model / table. These sources may then
		    | be assigned to any extra authentication guards you have defined.
		    |
		    | Supported: "database", "eloquent"
		    |
		    */

		    'providers' => [
		        'admins' => [
		            'driver' => 'eloquent',
		            'model' => App\Admin::class,
		        ],

		        // 'users' => [
		        //     'driver' => 'database',
		        //     'table' => 'users',
		        // ],
		    ],

		    /*
		    |--------------------------------------------------------------------------
		    | Resetting Passwords
		    |--------------------------------------------------------------------------
		    |
		    | You may specify multiple password reset configurations if you have more
		    | than one user table or model in the application and you want to have
		    | separate password reset settings based on the specific user types.
		    |
		    | The expire time is the number of minutes that the reset token should be
		    | considered valid. This security feature keeps tokens short-lived so
		    | they have less time to be guessed. You may change this as needed.
		    |
		    */

		    'passwords' => [
		        'admins' => [
		            'provider' => 'admins',
		            'table' => 'password_resets',
		            'expire' => 60,
		            'throttle' => 60,
		        ],
		    ],

		    /*
		    |--------------------------------------------------------------------------
		    | Password Confirmation Timeout
		    |--------------------------------------------------------------------------
		    |
		    | Here you may define the amount of seconds before a password confirmation
		    | times out and the user is prompted to re-enter their password via the
		    | confirmation screen. By default, the timeout lasts for three hours.
		    |
		    */

		    'password_timeout' => 10800,

		];
		