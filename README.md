# laravel-eloquent-sql-queries
Laravel all sql queries and eloquent quires

<hr>
    
**Use DB::table('table_name') or Model:: for all quires **  
  
 #### #INSERT: 

        1)  $data['email']  =  'kayla@example.com';
            $data['status']  =  1;
            DB::table('users')->insert($data);

        2)  DB::table('users')->insert([
                'email' => 'kayla@example.com',
                'status' => 0
            ]);

        3)  DB::table('users')->insertOrIgnore([ //Ignore errors 
                ['id' => 1, 'email' => 'sisko@example.com'],
                ['id' => 2, 'email' => 'archer@example.com'],
            ]);

        4)  DB::table('users')->insertGetId($data); //returns ID
        <!-- Using Eloquent model -->
        5)  User::create($data);
        6)  $user = new User;
            $user->name = $request->name;
            $user->mobile = $request->mobile;
            $user->save();

        *Note: you will need to specify either a fillable or guarded property on your model class.
        protected $fillable = ['name','mobile']; //In model 
        protected $guarded = []; // set empty for mass assignment.
    
  #### #UPDATE: 

        1) $update = DB::table('users')->where('id', 1)->update(['votes' => 1]);
        2) $update = DB::table('users')
                    ->updateOrInsert(
                        ['email' => 'john@example.com', 'name' => 'John'],  //check with these fileds, if exits update else create new
                        ['votes' => '2']
                    );
        //create or update
        3) DB::table('flights')->upsert($data, ['email']); //check with email

 #### #DELETE:

        1) DB::table('users')->delete();
        2) DB::table('users')->where('id', 100)->delete();
        3) DB::table('users')->truncate(); //Empty the table and increment values set 0
        
 #### #ALL RECORDS:

        1) DB::table('users')->get();
        2) User::all(); //using eloquent model
        3) DB::table('users')->select('*')->get();
        4) DB::table('users')->select('name','id','marks as user_marks')->get();

 #### #USING WHERE: 

        1) DB::table('users')->where('id',1)->first();
        2) User::where('status',1)->get();
        3) User::firstWhere('active', 1);
        4) User::where('marks', '>', 50)->firstOrFail();

 #### #USING FIND:

        1) User::find(3); //using primary key
        2) DB::table('users')->find(3);
        3) User::findOrFail(1); //return exception if not found 

 #### #RAW QUERIES:

        1) DB::table('orders')->whereRaw('price > IF(state = "TX", ?, 100)', [200])->get();
        
        2) $users = DB::table('users')
                    ->select(DB::raw('count(*) as user_count, status'))
                    ->where('status', '<>', 1)
                    ->groupBy('status')
                    ->get();

        3) $orders = DB::table('orders')
                    ->select('department', DB::raw('SUM(price) as total_sales'))
                    ->groupBy('department')
                    ->havingRaw('SUM(price) > ?', [2500])
                    ->get();


        4) $orders = DB::table('orders')
                    ->orderByRaw('updated_at - created_at DESC')
                    ->get();

        5) $orders = DB::table('orders')
                    ->select('city', 'state')
                    ->groupByRaw('city, state')
                    ->get();


 #### #JOINS:

        1) $users = DB::table('users as t1')
                    ->join('contacts as t2', 't1.id', '=', 't2.user_id')
                    ->join('orders as t3', 't1.id', '=', 't3.user_id')
                    ->select('t1.*', 't2.phone', 't3.price')
                    ->get();

        2) $users = DB::table('users')
                    ->leftJoin('posts', 'users.id', '=', 'posts.user_id')
                    ->get();

        3) $users = DB::table('users')
                    ->rightJoin('posts', 'users.id', '=', 'posts.user_id')
                    ->get();

        4) $sizes = DB::table('sizes')
                    ->crossJoin('colors')
                    ->get();

 #### #WHERE EXAMPLES:

        1) $users = DB::table('users')
                    ->where('name', 'like', '%'.$name.'%')
                    ->get();

        2) $users = DB::table('users')->where([
                        ['status', '=', '1'],
                        ['subscribed', '<>', '1'],
                    ])->get();

        3) $users = DB::table('users')
                    ->where('votes', '>', 100)
                    ->orWhere('name', 'John')
                    ->get();


 #### #WHERE BETWEEN:

        1) $users = DB::table('users')->whereBetween('votes', [1, 100])->get();
        2) $users = DB::table('users')->whereNotBetween('votes', [1, 100])->get();

 #### #WHERE IN & NOT IN:

        1) $users = DB::table('users')->whereIn('id', [1, 2, 3])->get();
        2) $users = DB::table('users')->whereNotIn('id', [1, 2, 3])->get();

 #### #WHERE NULL & NOT NULL:

        1) $users = DB::table('users')->whereNull('updated_at')->get();
        2) $users = DB::table('users')->whereNotNull('updated_at')->get();

 #### #DATES:

        1) $users = DB::table('users')->whereDate('created_at', '2016-12-31')->get();
        2) $users = DB::table('users')->whereMonth('created_at', '12')->get();
        3) $users = DB::table('users')->whereDay('created_at', '31')->get();
        4) $users = DB::table('users')->whereYear('created_at', '2016')->get();
        5) $users = DB::table('users')->whereTime('created_at', '=', '11:20:45')->get();

 #### #Two columns equal or not:

        1) $users = DB::table('users')->whereColumn('first_name','=','last_name')->get();

 #### #ORDER BY:

        1) $users = DB::table('users')->orderBy('name', 'desc')->get();
        1) $users = DB::table('users')->orderBy('name', 'asc')->get();
        1) $users = DB::table('users')->orderByDesc('name')->get();
        2) $user = DB::table('users')->latest()->get(); #order by default created_at 
        2) $user = DB::table('users')->oldest()->first();  #order by default created_at

 #### #HAVING & GROUPBY:
    
        1) $users = DB::table('users')
                        ->groupBy('account_id')
                        ->having('account_id', '>', 100)
                        ->get();

        2) $report = DB::table('orders')
                    ->selectRaw('count(id) as number_of_orders, customer_id')
                    ->groupBy('customer_id')
                    ->havingBetween('number_of_orders', [5, 15])
                    ->get();

        3) $users = DB::table('users')
                    ->groupBy('first_name', 'status')
                    ->having('account_id', '>', 100)
                    ->get();

 #### #Limit & Offset:

        1) $users = DB::table('users')->offset(10)->limit(5)->get();
        2) $users = DB::table('users')->skip(10)->take(5)->get();

 #### #WHEN:

        Used for multiple conditions at a time. WHEN method only executes the given closure when the first argument is true.

        $search = $request->input('search_key');
        $order = $request->input('order');
        $role = $request->input('role');

        $users = DB::table('users')
                        ->when($search, function ($query, $search) {
                            return $query->where('votes','like','%'.$search.'%');
                        })
                        ->when($order, function ($query, $order) {
                            return $query->where('order',$order);
                        })
                        ->when($role, function ($query, $role) {
                            return $query->where('role_id', $role);
                        })
                        ->get();
