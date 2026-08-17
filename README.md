# finanzas-hogar-pro
finanzas-hogar-pro
lib/
├── import 'package:flutter/material.dart';
import 'app.dart';

void main() {
  WidgetsFlutterBinding.ensureInitialized();
  runApp(const FinanzasHogarPro());
}
├── app.dart
import 'package:flutter/material.dart';
import 'screens/dashboard/dashboard_screen.dart';

class FinanzasHogarPro extends StatelessWidget {
  const FinanzasHogarPro({super.key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      debugShowCheckedModeBanner: false,
      title: 'Finanzas Hogar Pro',
      theme: ThemeData(
        useMaterial3: true,
        brightness: Brightness.light,
      ),
      home: const DashboardScreen(),
    );
  }
}

├── models/
├── services/
├── database/
├── screens/
│   ├── dashboard/
│   ├── movimientos/
│   └── cuentas/
└── widgets/
lib/screens/dashboard/dashboard_screen.dart: import 'package:flutter/material.dart';

class DashboardScreen extends StatelessWidget {
  const DashboardScreen({super.key});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text('Finanzas Hogar Pro'),
      ),
      body: Padding(
        padding: const EdgeInsets.all(16),
        child: Column(
          crossAxisAlignment: CrossAxisAlignment.start,
          children: [
            const Text(
              'Resumen financiero',
              style: TextStyle(
                fontSize: 24,
                fontWeight: FontWeight.bold,
              ),
            ),
            const SizedBox(height: 20),

            Card(
              child: Padding(
                padding: const EdgeInsets.all(20),
                child: Column(
                  crossAxisAlignment: CrossAxisAlignment.start,
                  children: const [
                    Text('Saldo disponible'),
                    SizedBox(height: 8),
                    Text(
                      '₡0',
                      style: TextStyle(
                        fontSize: 32,
                        fontWeight: FontWeight.bold,
                      ),
                    ),
                  ],
                ),
              ),
            ),

            const SizedBox(height: 16),

            Row(
              children: [
                Expanded(
                  child: _ResumenCard(
                    titulo: 'Ingresos',
                    monto: '₡0',
                  ),
                ),
                const SizedBox(width: 12),
                Expanded(
                  child: _ResumenCard(
                    titulo: 'Gastos',
                    monto: '₡0',
                  ),
                ),
              ],
            ),

            const SizedBox(height: 12),

            Row(
              children: [
                Expanded(
                  child: _ResumenCard(
                    titulo: 'Ahorro',
                    monto: '₡0',
                  ),
                ),
                const SizedBox(width: 12),
                Expanded(
                  child: _ResumenCard(
                    titulo: 'Deudas',
                    monto: '₡0',
                  ),
                ),
              ],
            ),

            const Spacer(),

            SizedBox(
              width: double.infinity,
              child: FilledButton.icon(
                onPressed: () {},
                icon: const Icon(Icons.add),
                label: const Text('Agregar movimiento'),
              ),
            ),
          ],
        ),
      ),
    );
  }
}

class _ResumenCard extends StatelessWidget {
  final String titulo;
  final String monto;

  const _ResumenCard({
    required this.titulo,
    required this.monto,
  });

  @override
  Widget build(BuildContext context) {
    return Card(
      child: Padding(
        padding: const EdgeInsets.all(16),
        child: Column(
          crossAxisAlignment: CrossAxisAlignment.start,
          children: [
            Text(titulo),
            const SizedBox(height: 6),
            Text(
              monto,
              style: const TextStyle(
                fontSize: 20,
                fontWeight: FontWeight.bold,
              ),
            ),
          ],
        ),
      ),
    );
  }
}
flutter analyze
flutter run
ACTIVO
  ├── Efectivo
  ├── Bancos
  ├── Ahorros
  └── Inversiones

PASIVO
  ├── Tarjetas de crédito
  ├── Préstamos
  └── Hipotecas

INGRESOS
  ├── Salarios
  ├── Negocios
  └── Otros

GASTOS
  ├── Alimentación
  ├── Vivienda
  ├── Transporte
  └── Otros
  accounts
categories
transactions
transfers
budgets
debts
assets
goals
pubspec.yaml: dependencies:
  flutter:
    sdk: flutter

  sqflite: ^2.3.3+1
  path: ^1.9.0
  lib/
├── database/
│   └── app_database.dart
├── models/
│   ├── account.dart
│   ├── category.dart
│   └── transaction.dart
├── services/
│   └── finance_service.dart
└── screens/
    ├── dashboard/
    ├── accounts/
    └── movements/
    lib/database/app_database.dart: import 'package:path/path.dart';
import 'package:sqflite/sqflite.dart';

class AppDatabase {
  static Database? _database;

  static Future<Database> get database async {
    if (_database != null) return _database!;

    _database = await _initDatabase();
    return _database!;
  }

  static Future<Database> _initDatabase() async {
    final dbPath = await getDatabasesPath();

    final path = join(
      dbPath,
      'finanzas_hogar_pro.db',
    );

    return openDatabase(
      path,
      version: 1,
      onCreate: (db, version) async {
        await db.execute('''
          CREATE TABLE accounts (
            id INTEGER PRIMARY KEY AUTOINCREMENT,
            name TEXT NOT NULL,
            type TEXT NOT NULL,
            currency TEXT NOT NULL DEFAULT 'CRC',
            initial_balance REAL NOT NULL DEFAULT 0,
            active INTEGER NOT NULL DEFAULT 1
          )
        ''');

        await db.execute('''
          CREATE TABLE categories (
            id INTEGER PRIMARY KEY AUTOINCREMENT,
            name TEXT NOT NULL,
            type TEXT NOT NULL,
            active INTEGER NOT NULL DEFAULT 1
          )
        ''');

        await db.execute('''
          CREATE TABLE transactions (
            id INTEGER PRIMARY KEY AUTOINCREMENT,
            account_id INTEGER NOT NULL,
            category_id INTEGER,
            type TEXT NOT NULL,
            amount REAL NOT NULL,
            description TEXT,
            transaction_date TEXT NOT NULL,
            FOREIGN KEY (account_id)
              REFERENCES accounts(id),
            FOREIGN KEY (category_id)
              REFERENCES categories(id)
          )
        ''');

        await db.execute('''
          CREATE TABLE transfers (
            id INTEGER PRIMARY KEY AUTOINCREMENT,
            source_account_id INTEGER NOT NULL,
            destination_account_id INTEGER NOT NULL,
            amount REAL NOT NULL,
            transfer_date TEXT NOT NULL,
            description TEXT,
            FOREIGN KEY (source_account_id)
              REFERENCES accounts(id),
            FOREIGN KEY (destination_account_id)
              REFERENCES accounts(id)
          )
        ''');

        await db.execute('''
          CREATE TABLE budgets (
            id INTEGER PRIMARY KEY AUTOINCREMENT,
            category_id INTEGER NOT NULL,
            amount REAL NOT NULL,
            month INTEGER NOT NULL,
            year INTEGER NOT NULL,
            FOREIGN KEY (category_id)
              REFERENCES categories(id)
          )
        ''');

        await db.execute('''
          CREATE TABLE debts (
            id INTEGER PRIMARY KEY AUTOINCREMENT,
            name TEXT NOT NULL,
            original_amount REAL NOT NULL,
            balance REAL NOT NULL,
            interest_rate REAL NOT NULL DEFAULT 0,
            payment REAL NOT NULL DEFAULT 0,
            due_date TEXT
          )
        ''');

        await db.execute('''
          CREATE TABLE assets (
            id INTEGER PRIMARY KEY AUTOINCREMENT,
            name TEXT NOT NULL,
            type TEXT NOT NULL,
            purchase_value REAL NOT NULL,
            current_value REAL NOT NULL
          )
        ''');

        await db.execute('''
          CREATE TABLE goals (
            id INTEGER PRIMARY KEY AUTOINCREMENT,
            name TEXT NOT NULL,
            target_amount REAL NOT NULL,
            saved_amount REAL NOT NULL DEFAULT 0,
            target_date TEXT
          )
        ''');
      },
    );
  }
}
lib/services/finance_service.dart: import '../database/app_database.dart';

class FinanceService {
  Future<int> createAccount({
    required String name,
    required String type,
    required double initialBalance,
  }) async {
    final db = await AppDatabase.database;

    return db.insert(
      'accounts',
      {
        'name': name,
        'type': type,
        'currency': 'CRC',
        'initial_balance': initialBalance,
        'active': 1,
      },
    );
  }

  Future<List<Map<String, dynamic>>> getAccounts() async {
    final db = await AppDatabase.database;

    return db.query(
      'accounts',
      where: 'active = ?',
      whereArgs: [1],
      orderBy: 'name ASC',
    );
  }

  Future<int> createTransaction({
    required int accountId,
    int? categoryId,
    required String type,
    required double amount,
    required String description,
  }) async {
    final db = await AppDatabase.database;

    return db.insert(
      'transactions',
      {
        'account_id': accountId,
        'category_id': categoryId,
        'type': type,
        'amount': amount,
        'description': description,
        'transaction_date':
            DateTime.now().toIso8601String(),
      },
    );
  }
}
Saldo actual =
Saldo inicial
+ ingresos
- gastos
+ transferencias recibidas
- transferencias enviadas
- flutter analyze
- flutter run
transaction_splits
CREATE TABLE transaction_splits (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    transaction_id INTEGER NOT NULL,
    account_id INTEGER NOT NULL,
    amount REAL NOT NULL,
    entry_type TEXT NOT NULL, -- DEBIT o CREDIT
    FOREIGN KEY(transaction_id) REFERENCES transactions(id),
    FOREIGN KEY(account_id) REFERENCES accounts(id)

);Dashboard
│
├── Cuentas
│     ├── Lista
│     ├── Nueva cuenta
│     └── Editar cuenta
│
├── Movimientos
│     ├── Lista
│     ├── Nuevo ingreso
│     ├── Nuevo gasto
│     └── Transferencia
│
└── Configuración
Saldo Disponible

Ingresos      Gastos

Ahorro        Deudas

Patrimonio    Presupuesto
Banco Nacional

Banco

CRC

₡1 250 000

Activo
users
------
id
name
email
password_hash
created_at
user_id
accounts
transactions
attachments
attachments
------------
id
transaction_id
file_path
file_type
created_at
updated_at
deleted_at
transactions(account_id)
transactions(transaction_date)
categories(type)
budgets(category_id)
account_types
payment_methods
financial_institutions
recurring_transactions
exchange_rates
currencies
-----------
CRC
USD
```dart
class Currency {
  final int? id;
  final String code;
  final String name;
  final String symbol;
  final int decimals;
  final bool active;

  const Currency({
    this.id,
    required this.code,
    required this.name,
    required this.symbol,
    this.decimals = 2,
    this.active = true,
  });

  Map<String, dynamic> toMap() {
    return {
      'id': id,
      'code': code,
      'name': name,
      'symbol': symbol,
      'decimals': decimals,
      'active': active ? 1 : 0,
    };
  }

  factory Currency.fromMap(Map<String, dynamic> map) {
    return Currency(
      id: map['id'] as int?,
      code: map['code'] as String,
      name: map['name'] as String,
      symbol: map['symbol'] as String,
      decimals: map['decimals'] as int? ?? 2,
      active: (map['active'] as int? ?? 1) == 1,
    );
  }
}
```
lib/database/app_database.dart
onCreate
```dart
await db.execute('''
  CREATE TABLE currencies (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    code TEXT NOT NULL UNIQUE,
    name TEXT NOT NULL,
    symbol TEXT NOT NULL,
    decimals INTEGER NOT NULL DEFAULT 2,
    active INTEGER NOT NULL DEFAULT 1
  )
''');

await db.insert('currencies', {
  'code': 'CRC',
  'name': 'Colón costarricense',
  'symbol': '₡',
  'decimals': 2,
  'active': 1,
});

await db.insert('currencies', {
  'code': 'USD',
  'name': 'Dólar estadounidense',
  'symbol': '\$',
  'decimals': 2,
  'active': 1,
});

await db.insert('currencies', {
  'code': 'EUR',
  'name': 'Euro',
  'symbol': '€',
  'decimals': 2,
  'active': 1,
});

await db.insert('currencies', {
  'code': 'GBP',
  'name': 'Libra esterlina',
  'symbol': '£',
  'decimals': 2,
  'active': 1,
});
```
lib/services/currency_service.dart
```dart
import '../database/app_database.dart';
import '../models/currency.dart';

class CurrencyService {
  Future<List<Currency>> getActiveCurrencies() async {
    final db = await AppDatabase.database;

    final result = await db.query(
      'currencies',
      where: 'active = ?',
      whereArgs: [1],
      orderBy: 'code ASC',
    );

    return result
        .map((map) => Currency.fromMap(map))
        .toList();
  }

  Future<Currency?> getCurrencyByCode(String code) async {
    final db = await AppDatabase.database;

    final result = await db.query(
      'currencies',
      where: 'code = ?',
      whereArgs: [code],
      limit: 1,
    );

    if (result.isEmpty) {
      return null;
    }

    return Currency.fromMap(result.first);
  }

  Future<int> createCurrency({
    required String code,
    required String name,
    required String symbol,
    int decimals = 2,
  }) async {
    final db = await AppDatabase.database;

    return db.insert(
      'currencies',
      {
        'code': code.toUpperCase(),
        'name': name,
        'symbol': symbol,
        'decimals': decimals,
        'active': 1,
      },
      conflictAlgorithm: ConflictAlgorithm.abort,
    );
  }

  Future<int> deactivateCurrency(int id) async {
    final db = await AppDatabase.database;

    return db.update(
      'currencies',
      {'active': 0},
      where: 'id = ?',
      whereArgs: [id],
    );
  }
}
```
accounts
currency TEXT NOT NULL DEFAULT 'CRC'
```dart
await db.execute('''
  CREATE TABLE accounts (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    name TEXT NOT NULL,
    type TEXT NOT NULL,
    currency_id INTEGER NOT NULL,
    initial_balance REAL NOT NULL DEFAULT 0,
    active INTEGER NOT NULL DEFAULT 1,
    created_at TEXT NOT NULL,
    updated_at TEXT NOT NULL,
    FOREIGN KEY (currency_id)
      REFERENCES currencies(id)
  )
''');
```
flutter clean
flutter pub get
flutter run
Base de datos v1
       ↓
Base de datos v2
       ↓
Base de datos v3
       ↓
Base de datos v4
lib/
├── database/
│   └── app_database.dart
│
├── models/
│   └── currency.dart
│
├── services/
│   ├── finance_service.dart
│   └── currency_service.dart
│
└── screens/
    ├── dashboard/
    ├── accounts/
    └── movements/
    MONEDAS
│
├── CRC - Colón costarricense ₡
├── USD - Dólar estadounidense $
├── EUR - Euro €
└── GBP - Libra esterlina £
Gasto                 ₡50.000
      Tarjeta de crédito       ₡50.00
      Tarjeta de crédito    ₡50.000
      Banco                     ₡50.000
      Activos - Pasivos
      Entradas de efectivo - Salidas de efectivo
      accounts
├── id
├── name
├── type
├── currency_id
├── initial_balance
├── active
├── created_at
└── updated_at
currencies
├── CRC
├── USD
├── EUR
└── GBP
Nombre de cuenta
[ Banco Nacional             ]

Tipo
[ Cuenta de ahorro        ▼ ]

Moneda
[ CRC - ₡                 ▼ ]

Saldo inicial
[ ₡ 500.000                  ]

[ CANCELAR ]     [ GUARDAR ]
class Account {
  final int? id;
  final String name;
  final String type;
  final int currencyId;
  final double initialBalance;
  final bool active;
  final DateTime createdAt;
  final DateTime updatedAt;

  const Account({
    this.id,
    required this.name,
    required this.type,
    required this.currencyId,
    required this.initialBalance,
    this.active = true,
    required this.createdAt,
    required this.updatedAt,
  });

  Map<String, dynamic> toMap() {
    return {
      'id': id,
      'name': name,
      'type': type,
      'currency_id': currencyId,
      'initial_balance': initialBalance,
      'active': active ? 1 : 0,
      'created_at': createdAt.toIso8601String(),
      'updated_at': updatedAt.toIso8601String(),
    };
  }

  factory Account.fromMap(Map<String, dynamic> map) {
    return Account(
      id: map['id'] as int?,
      name: map['name'] as String,
      type: map['type'] as String,
      currencyId: map['currency_id'] as int,
      initialBalance:
          (map['initial_balance'] as num).toDouble(),
      active: (map['active'] as int? ?? 1) == 1,
      createdAt:
          DateTime.parse(map['created_at'] as String),
      updatedAt:
          DateTime.parse(map['updated_at'] as String),
    );
  }
}
lib/screens/accounts/accounts_screen.dart
import 'package:sqflite/sqflite.dart';

import '../database/app_database.dart';
import '../models/account.dart';

class FinanceService {
  Future<int> createAccount({
    required String name,
    required String type,
    required int currencyId,
    required double initialBalance,
  }) async {
    final db = await AppDatabase.database;

    final now = DateTime.now().toIso8601String();

    return db.insert(
      'accounts',
      {
        'name': name.trim(),
        'type': type,
        'currency_id': currencyId,
        'initial_balance': initialBalance,
        'active': 1,
        'created_at': now,
        'updated_at': now,
      },
    );
  }

  Future<List<Account>> getAccounts() async {
    final db = await AppDatabase.database;

    final result = await db.query(
      'accounts',
      where: 'active = ?',
      whereArgs: [1],
      orderBy: 'name ASC',
    );

    return result
        .map(Account.fromMap)
        .toList();
  }

  Future<int> deactivateAccount(int id) async {
    final db = await AppDatabase.database;

    return db.update(
      'accounts',
      {
        'active': 0,
        'updated_at':
            DateTime.now().toIso8601String(),
      },
      where: 'id = ?',
      whereArgs: [id],
    );
  }
}
import 'package:flutter/material.dart';

import '../../models/account.dart';
import '../../services/finance_service.dart';
import 'new_account_screen.dart';

class AccountsScreen extends StatefulWidget {
  const AccountsScreen({super.key});

  @override
  State<AccountsScreen> createState() =>
      _AccountsScreenState();
}

class _AccountsScreenState
    extends State<AccountsScreen> {
  final FinanceService _service = FinanceService();

  List<Account> _accounts = [];
  bool _loading = true;

  @override
  void initState() {
    super.initState();
    _loadAccounts();
  }

  Future<void> _loadAccounts() async {
    setState(() {
      _loading = true;
    });

    final accounts = await _service.getAccounts();

    if (!mounted) return;

    setState(() {
      _accounts = accounts;
      _loading = false;
    });
  }

  IconData _getIcon(String type) {
    switch (type) {
      case 'Efectivo':
        return Icons.payments_outlined;
      case 'Cuenta corriente':
        return Icons.account_balance;
      case 'Cuenta de ahorro':
        return Icons.savings_outlined;
      case 'Tarjeta de crédito':
        return Icons.credit_card;
      case 'SINPE':
        return Icons.phone_android;
      case 'Inversión':
        return Icons.trending_up;
      default:
        return Icons.account_balance_wallet_outlined;
    }
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text('Mis cuentas'),
      ),
      body: _loading
          ? const Center(
              child: CircularProgressIndicator(),
            )
          : _accounts.isEmpty
              ? _EmptyState(
                  onAdd: () async {
                    await Navigator.push(
                      context,
                      MaterialPageRoute(
                        builder: (_) =>
                            const NewAccountScreen(),
                      ),
                    );

                    _loadAccounts();
                  },
                )
              : RefreshIndicator(
                  onRefresh: _loadAccounts,
                  child: ListView.builder(
                    padding: const EdgeInsets.all(16),
                    itemCount: _accounts.length,
                    itemBuilder: (context, index) {
                      final account =
                          _accounts[index];

                      return Card(
                        margin:
                            const EdgeInsets.only(
                          bottom: 12,
                        ),
                        child: ListTile(
                          leading: CircleAvatar(
                            child: Icon(
                              _getIcon(account.type),
                            ),
                          ),
                          title: Text(
                            account.name,
                            style: const TextStyle(
                              fontWeight:
                                  FontWeight.bold,
                            ),
                          ),
                          subtitle:
                              Text(account.type),
                          trailing: Text(
                            '₡${account.initialBalance.toStringAsFixed(2)}',
                            style: const TextStyle(
                              fontWeight:
                                  FontWeight.bold,
                            ),
                          ),
                        ),
                      );
                    },
                  ),
                ),
      floatingActionButton: _accounts.isNotEmpty
          ? FloatingActionButton.extended(
              onPressed: () async {
                await Navigator.push(
                  context,
                  MaterialPageRoute(
                    builder: (_) =>
                        const NewAccountScreen(),
                  ),
                );

                _loadAccounts();
              },
              icon: const Icon(Icons.add),
              label: const Text('Cuenta'),
            )
          : null,
    );
  }
}

class _EmptyState extends StatelessWidget {
  final VoidCallback onAdd;

  const _EmptyState({
    required this.onAdd,
  });

  @override
  Widget build(BuildContext context) {
    return Center(
      child: Padding(
        padding: const EdgeInsets.all(32),
        child: Column(
          mainAxisAlignment:
              MainAxisAlignment.center,
          children: [
            const Icon(
              Icons.account_balance_wallet_outlined,
              size: 72,
            ),
            const SizedBox(height: 20),
            const Text(
              'Todavía no tienes cuentas',
              style: TextStyle(
                fontSize: 20,
                fontWeight: FontWeight.bold,
              ),
            ),
            const SizedBox(height: 10),
            const Text(
              'Agrega tu primera cuenta para comenzar '
              'a administrar tus finanzas.',
              textAlign: TextAlign.center,
            ),
            const SizedBox(height: 24),
            FilledButton.icon(
              onPressed: onAdd,
              icon: const Icon(Icons.add),
              label: const Text(
                'Agregar primera cuenta',
              ),
            ),
          ],
        ),
      ),
    );
  }
}
lib/screens/accounts/new_account_screen.dart
import 'package:flutter/material.dart';

import '../../models/currency.dart';
import '../../services/currency_service.dart';
import '../../services/finance_service.dart';

class NewAccountScreen extends StatefulWidget {
  const NewAccountScreen({super.key});

  @override
  State<NewAccountScreen> createState() =>
      _NewAccountScreenState();
}

class _NewAccountScreenState
    extends State<NewAccountScreen> {
  final _formKey = GlobalKey<FormState>();

  final _nameController = TextEditingController();
  final _balanceController =
      TextEditingController();

  final FinanceService _financeService =
      FinanceService();

  final CurrencyService _currencyService =
      CurrencyService();

  final List<String> _types = [
    'Efectivo',
    'Cuenta corriente',
    'Cuenta de ahorro',
    'Tarjeta de crédito',
    'SINPE',
    'Inversión',
    'Otro',
  ];

  List<Currency> _currencies = [];

  String _selectedType = 'Efectivo';
  Currency? _selectedCurrency;

  bool _loading = true;
  bool _saving = false;

  @override
  void initState() {
    super.initState();
    _loadCurrencies();
  }

  Future<void> _loadCurrencies() async {
    final currencies =
        await _currencyService
            .getActiveCurrencies();

    if (!mounted) return;

    setState(() {
      _currencies = currencies;

      if (currencies.isNotEmpty) {
        _selectedCurrency =
            currencies.firstWhere(
          (currency) =>
              currency.code == 'CRC',
          orElse: () => currencies.first,
        );
      }

      _loading = false;
    });
  }

  Future<void> _saveAccount() async {
    if (!_formKey.currentState!.validate()) {
      return;
    }

    if (_selectedCurrency == null) {
      ScaffoldMessenger.of(context).showSnackBar(
        const SnackBar(
          content: Text(
            'Seleccione una moneda.',
          ),
        ),
      );
      return;
    }

    setState(() {
      _saving = true;
    });

    try {
      final balance = double.parse(
        _balanceController.text
            .replaceAll('.', '')
            .replaceAll(',', '.'),
      );

      await _financeService.createAccount(
        name: _nameController.text,
        type: _selectedType,
        currencyId: _selectedCurrency!.id!,
        initialBalance: balance,
      );

      if (!mounted) return;

      Navigator.pop(context);
    } catch (e) {
      if (!mounted) return;

      ScaffoldMessenger.of(context).showSnackBar(
        SnackBar(
          content: Text(
            'No se pudo guardar la cuenta: $e',
          ),
        ),
      );
    } finally {
      if (mounted) {
        setState(() {
          _saving = false;
        });
      }
    }
  }

  @override
  void dispose() {
    _nameController.dispose();
    _balanceController.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text('Nueva cuenta'),
      ),
      body: _loading
          ? const Center(
              child: CircularProgressIndicator(),
            )
          : Form(
              key: _formKey,
              child: ListView(
                padding: const EdgeInsets.all(20),
                children: [
                  TextFormField(
                    controller: _nameController,
                    decoration:
                        const InputDecoration(
                      labelText: 'Nombre de la cuenta',
                      hintText:
                          'Ej. Banco Nacional',
                      border: OutlineInputBorder(),
                      prefixIcon:
                          Icon(Icons.account_balance),
                    ),
                    validator: (value) {
                      if (value == null ||
                          value.trim().isEmpty) {
                        return 'Ingrese el nombre de la cuenta';
                      }

                      return null;
                    },
                  ),

                  const SizedBox(height: 20),

                  DropdownButtonFormField<String>(
                    value: _selectedType,
                    decoration:
                        const InputDecoration(
                      labelText: 'Tipo de cuenta',
                      border: OutlineInputBorder(),
                    ),
                    items: _types
                        .map(
                          (type) =>
                              DropdownMenuItem(
                            value: type,
                            child: Text(type),
                          ),
                        )
                        .toList(),
                    onChanged: (value) {
                      if (value != null) {
                        setState(() {
                          _selectedType =
                              value;
                        });
                      }
                    },
                  ),

                  const SizedBox(height: 20),

                  DropdownButtonFormField<Currency>(
                    value: _selectedCurrency,
                    decoration:
                        const InputDecoration(
                      labelText: 'Moneda',
                      border: OutlineInputBorder(),
                    ),
                    items: _currencies
                        .map(
                          (currency) =>
                              DropdownMenuItem(
                            value: currency,
                            child: Text(
                              '${currency.code} - '
                              '${currency.name} '
                              '${currency.symbol}',
                            ),
                          ),
                        )
                        .toList(),
                    onChanged: (value) {
                      setState(() {
                        _selectedCurrency =
                            value;
                      });
                    },
                    validator: (value) {
                      if (value == null) {
                        return 'Seleccione una moneda';
                      }

                      return null;
                    },
                  ),

                  const SizedBox(height: 20),

                  TextFormField(
                    controller:
                        _balanceController,
                    keyboardType:
                        const TextInputType
                            .numberWithOptions(
                      decimal: true,
                    ),
                    decoration:
                        const InputDecoration(
                      labelText:
                          'Saldo inicial',
                      hintText: '0.00',
                      border: OutlineInputBorder(),
                      prefixIcon:
                          Icon(Icons.payments),
                    ),
                    validator: (value) {
                      if (value == null ||
                          value.trim().isEmpty) {
                        return 'Ingrese el saldo inicial';
                      }

                      final number =
                          double.tryParse(
                        value
                            .replaceAll('.', '')
                            .replaceAll(',', '.'),
                      );

                      if (number == null) {
                        return 'Ingrese un monto válido';
                      }

                      if (number < 0) {
                        return 'El saldo no puede ser negativo';
                      }

                      return null;
                    },
                  ),

                  const SizedBox(height: 32),

                  SizedBox(
                    height: 52,
                    child: FilledButton.icon(
                      onPressed:
                          _saving
                              ? null
                              : _saveAccount,
                      icon: _saving
                          ? const SizedBox(
                              width: 20,
                              height: 20,
                              child:
                                  CircularProgressIndicator(
                                strokeWidth: 2,
                              ),
                            )
                          : const Icon(
                              Icons.save,
                            ),
                      label: Text(
                        _saving
                            ? 'Guardando...'
                            : 'Guardar cuenta',
                      ),
                    ),
                  ),
                ],
              ),
            ),
    );
  }
}
dashboard_screen.dart
import '../accounts/accounts_screen.dart';
FilledButton.icon(
  onPressed: () {
    Navigator.push(
      context,
      MaterialPageRoute(
        builder: (_) => const AccountsScreen(),
      ),
    );
  },
  icon: const Icon(Icons.account_balance_wallet),
  label: const Text('Mis cuentas'),
)
flutter clean
flutter pub get
flutter analyze
flutter run
lib/models/financial_transaction.dart
class FinancialTransaction {
  final int? id;
  final int accountId;
  final int? categoryId;
  final String type;
  final double amount;
  final String description;
  final DateTime transactionDate;

  const FinancialTransaction({
    this.id,
    required this.accountId,
    this.categoryId,
    required this.type,
    required this.amount,
    required this.description,
    required this.transactionDate,
  });

  Map<String, dynamic> toMap() {
    return {
      'id': id,
      'account_id': accountId,
      'category_id': categoryId,
      'type': type,
      'amount': amount,
      'description': description,
      'transaction_date':
          transactionDate.toIso8601String(),
    };
  }

  factory FinancialTransaction.fromMap(
    Map<String, dynamic> map,
  ) {
    return FinancialTransaction(
      id: map['id'] as int?,
      accountId: map['account_id'] as int,
      categoryId: map['category_id'] as int?,
      type: map['type'] as String,
      amount: (map['amount'] as num).toDouble(),
      description:
          map['description'] as String? ?? '',
      transactionDate:
          DateTime.parse(
        map['transaction_date'] as String,
      ),
    );
  }
}
lib/services/finance_service.dart
Future<int> createTransaction({
  required int accountId,
  int? categoryId,
  required String type,
  required double amount,
  required String description,
  DateTime? transactionDate,
}) async {
  final db = await AppDatabase.database;

  return db.insert(
    'transactions',
    {
      'account_id': accountId,
      'category_id': categoryId,
      'type': type,
      'amount': amount,
      'description': description.trim(),
      'transaction_date':
          (transactionDate ?? DateTime.now())
              .toIso8601String(),
    },
  );
}
Future<List<Map<String, dynamic>>> getTransactions() async {
  final db = await AppDatabase.database;

  return db.query(
    'transactions',
    orderBy: 'transaction_date DESC',
  );
}
Salario
Negocio
Servicios profesionales
Intereses
Otros ingresos

Alimentación
Vivienda
Transporte
Educación
Salud
Servicios públicos
Comunicaciones
Entretenimiento
Ropa
Seguros
Impuestos
Mantenimiento
Otros gastos
pp_database.dart
categories
final incomeCategories = [
  'Salario',
  'Negocio',
  'Servicios profesionales',
  'Intereses',
  'Otros ingresos',
];

for (final category in incomeCategories) {
  await db.insert('categories', {
    'name': category,
    'type': 'income',
    'active': 1,
  });
}

final expenseCategories = [
  'Alimentación',
  'Vivienda',
  'Transporte',
  'Educación',
  'Salud',
  'Servicios públicos',
  'Comunicaciones',
  'Entretenimiento',
  'Ropa',
  'Seguros',
  'Impuestos',
  'Mantenimiento',
  'Otros gastos',
];

for (final category in expenseCategories) {
  await db.insert('categories', {
    'name': category,
    'type': 'expense',
    'active': 1,
  });
}
lib/services/category_service.dart
import '../database/app_database.dart';

class CategoryService {
  Future<List<Map<String, dynamic>>> getCategories(
    String type,
  ) async {
    final db = await AppDatabase.database;

    return db.query(
      'categories',
      where: 'type = ? AND active = ?',
      whereArgs: [type, 1],
      orderBy: 'name ASC',
    );
  }
}
lib/screens/movements/new_transaction_screen.dart
import 'package:flutter/material.dart';

import '../../models/account.dart';
import '../../services/category_service.dart';
import '../../services/finance_service.dart';

class NewTransactionScreen extends StatefulWidget {
  const NewTransactionScreen({
    super.key,
  });

  @override
  State<NewTransactionScreen> createState() =>
      _NewTransactionScreenState();
}

class _NewTransactionScreenState
    extends State<NewTransactionScreen> {
  final _formKey =
      GlobalKey<FormState>();

  final _amountController =
      TextEditingController();

  final _descriptionController =
      TextEditingController();

  final FinanceService _financeService =
      FinanceService();

  final CategoryService _categoryService =
      CategoryService();

  List<Account> _accounts = [];

  List<Map<String, dynamic>> _categories =
      [];

  String _type = 'expense';

  Account? _selectedAccount;

  int? _selectedCategory;

  bool _loading = true;
  bool _saving = false;

  @override
  void initState() {
    super.initState();
    _loadData();
  }

  Future<void> _loadData() async {
    final accounts =
        await _financeService.getAccounts();

    final categories =
        await _categoryService
            .getCategories(_type);

    if (!mounted) return;

    setState(() {
      _accounts = accounts;
      _categories = categories;

      if (accounts.isNotEmpty) {
        _selectedAccount =
            accounts.first;
      }

      _loading = false;
    });
  }

  Future<void> _changeType(
    String value,
  ) async {
    setState(() {
      _type = value;
      _selectedCategory = null;
    });

    final categories =
        await _categoryService
            .getCategories(value);

    if (!mounted) return;

    setState(() {
      _categories = categories;
    });
  }

  Future<void> _save() async {
    if (!_formKey.currentState!.validate()) {
      return;
    }

    if (_selectedAccount == null) {
      return;
    }

    if (_selectedCategory == null) {
      ScaffoldMessenger.of(context)
          .showSnackBar(
        const SnackBar(
          content: Text(
            'Seleccione una categoría.',
          ),
        ),
      );
      return;
    }

    setState(() {
      _saving = true;
    });

    try {
      final amount = double.parse(
        _amountController.text
            .replaceAll('.', '')
            .replaceAll(',', '.'),
      );

      await _financeService
          .createTransaction(
        accountId:
            _selectedAccount!.id!,
        categoryId:
            _selectedCategory,
        type: _type,
        amount: amount,
        description:
            _descriptionController.text,
      );

      if (!mounted) return;

      Navigator.pop(context);
    } catch (e) {
      if (!mounted) return;

      ScaffoldMessenger.of(context)
          .showSnackBar(
        SnackBar(
          content: Text(
            'Error: $e',
          ),
        ),
      );
    } finally {
      if (mounted) {
        setState(() {
          _saving = false;
        });
      }
    }
  }

  @override
  void dispose() {
    _amountController.dispose();
    _descriptionController.dispose();
    super.dispose();
  }

  @override
  Widget build(
    BuildContext context,
  ) {
    return Scaffold(
      appBar: AppBar(
        title: const Text(
          'Nuevo movimiento',
        ),
      ),
      body: _loading
          ? const Center(
              child:
                  CircularProgressIndicator(),
            )
          : Form(
              key: _formKey,
              child: ListView(
                padding:
                    const EdgeInsets.all(20),
                children: [
                  SegmentedButton<String>(
                    segments: const [
                      ButtonSegment(
                        value: 'income',
                        label:
                            Text('Ingreso'),
                        icon: Icon(
                          Icons.arrow_downward,
                        ),
                      ),
                      ButtonSegment(
                        value: 'expense',
                        label:
                            Text('Gasto'),
                        icon: Icon(
                          Icons.arrow_upward,
                        ),
                      ),
                    ],
                    selected: {_type},
                    onSelectionChanged:
                        (selection) {
                      _changeType(
                        selection.first,
                      );
                    },
                  ),

                  const SizedBox(height: 24),

                  DropdownButtonFormField<Account>(
                    value:
                        _selectedAccount,
                    decoration:
                        const InputDecoration(
                      labelText: 'Cuenta',
                      border:
                          OutlineInputBorder(),
                    ),
                    items: _accounts
                        .map(
                          (account) =>
                              DropdownMenuItem(
                            value: account,
                            child: Text(
                              account.name,
                            ),
                          ),
                        )
                        .toList(),
                    onChanged:
                        (value) {
                      setState(() {
                        _selectedAccount =
                            value;
                      });
                    },
                  ),

                  const SizedBox(height: 20),

                  DropdownButtonFormField<int>(
                    value:
                        _selectedCategory,
                    decoration:
                        const InputDecoration(
                      labelText:
                          'Categoría',
                      border:
                          OutlineInputBorder(),
                    ),
                    items: _categories
                        .map(
                          (category) =>
                              DropdownMenuItem(
                            value:
                                category['id']
                                    as int,
                            child: Text(
                              category[
                                  'name'],
                            ),
                          ),
                        )
                        .toList(),
                    onChanged:
                        (value) {
                      setState(() {
                        _selectedCategory =
                            value;
                      });
                    },
                  ),

                  const SizedBox(height: 20),

                  TextFormField(
                    controller:
                        _amountController,
                    keyboardType:
                        const TextInputType
                            .numberWithOptions(
                      decimal: true,
                    ),
                    decoration:
                        const InputDecoration(
                      labelText: 'Monto',
                      border:
                          OutlineInputBorder(),
                      prefixText: '₡ ',
                    ),
                    validator: (value) {
                      if (value ==
                              null ||
                          value.isEmpty) {
                        return 'Ingrese el monto';
                      }

                      final amount =
                          double.tryParse(
                        value
                            .replaceAll(
                                '.', '')
                            .replaceAll(
                                ',', '.'),
                      );

                      if (amount == null ||
                          amount <= 0) {
                        return 'Ingrese un monto válido';
                      }

                      return null;
                    },
                  ),

                  const SizedBox(height: 20),

                  TextFormField(
                    controller:
                        _descriptionController,
                    decoration:
                        const InputDecoration(
                      labelText:
                          'Descripción',
                      hintText:
                          'Ej. Supermercado',
                      border:
                          OutlineInputBorder(),
                    ),
                    validator: (value) {
                      if (value ==
                              null ||
                          value
                              .trim()
                              .isEmpty) {
                        return 'Ingrese una descripción';
                      }

                      return null;
                    },
                  ),

                  const SizedBox(height: 32),

                  SizedBox(
                    height: 52,
                    child:
                        FilledButton.icon(
                      onPressed:
                          _saving
                              ? null
                              : _save,
                      icon: _saving
                          ? const SizedBox(
                              width: 20,
                              height: 20,
                              child:
                                  CircularProgressIndicator(
                                strokeWidth:
                                    2,
                              ),
                            )
                          : const Icon(
                              Icons.save,
                            ),
                      label: Text(
                        _saving
                            ? 'Guardando...'
                            : 'Guardar movimiento',
                      ),
                    ),
                  ),
                ],
              ),
            ),
    );
  }
}
dashboard_screen.dart
import '../movements/new_transaction_screen.dart';
onPressed: () {},
onPressed: () {
  Navigator.push(
    context,
    MaterialPageRoute(
      builder: (_) =>
          const NewTransactionScreen(),
    ),
  );
},
double
₡75.500,25
flutter clean
flutter pub get
flutter analyze
flutter run
Banco Nacional
Cuenta de ahorro
CRC
₡500.000
Ingreso
Banco Nacional
Salario
₡800.000
Gasto
Banco Nacional
Alimentación
₡75.000
Saldo inicial       ₡500.000
+ Salario           ₡800.000
- Alimentación       ₡75.000
-----------------------------
Saldo                ₡1.225.000
AccountBalance
lib/models/account_balance.dart
class AccountBalance {
  final int accountId;
  final double initialBalance;
  final double income;
  final double expenses;
  final double transfersReceived;
  final double transfersSent;

  const AccountBalance({
    required this.accountId,
    required this.initialBalance,
    required this.income,
    required this.expenses,
    required this.transfersReceived,
    required this.transfersSent,
  });

  double get currentBalance {
    return initialBalance +
        income -
        expenses +
        transfersReceived -
        transfersSent;
  }
}
FinanceService
lib/services/finance_service.dart
Future<double> getAccountBalance(
  int accountId,
) async {
  final db = await AppDatabase.database;

  final accounts = await db.query(
    'accounts',
    where: 'id = ?',
    whereArgs: [accountId],
    limit: 1,
  );

  if (accounts.isEmpty) {
    return 0;
  }

  final initialBalance =
      (accounts.first['initial_balance'] as num)
          .toDouble();

  final incomeResult = await db.rawQuery(
    '''
    SELECT COALESCE(SUM(amount), 0) AS total
    FROM transactions
    WHERE account_id = ?
    AND type = 'income'
    ''',
    [accountId],
  );

  final expenseResult = await db.rawQuery(
    '''
    SELECT COALESCE(SUM(amount), 0) AS total
    FROM transactions
    WHERE account_id = ?
    AND type = 'expense'
    ''',
    [accountId],
  );

  final transfersReceivedResult =
      await db.rawQuery(
    '''
    SELECT COALESCE(SUM(amount), 0) AS total
    FROM transfers
    WHERE destination_account_id = ?
    ''',
    [accountId],
  );

  final transfersSentResult =
      await db.rawQuery(
    '''
    SELECT COALESCE(SUM(amount), 0) AS total
    FROM transfers
    WHERE source_account_id = ?
    ''',
    [accountId],
  );

  final income =
      (incomeResult.first['total'] as num)
          .toDouble();

  final expenses =
      (expenseResult.first['total'] as num)
          .toDouble();

  final transfersReceived =
      (transfersReceivedResult.first['total'] as num)
          .toDouble();

  final transfersSent =
      (transfersSentResult.first['total'] as num)
          .toDouble();

  return initialBalance +
      income -
      expenses +
      transfersReceived -
      transfersSent;
}
Future<double> getTotalBalance() async {
  final accounts = await getAccounts();

  double total = 0;

  for (final account in accounts) {
    total += await getAccountBalance(
      account.id!,
    );
  }

  return total;
}
Banco Nacional       ₡1.225.000
Cuenta de ahorro       ₡300.000
Efectivo                ₡50.000
--------------------------------
Saldo total           ₡1.575.000
Future<double> getMonthlyIncome() async {
  final db = await AppDatabase.database;

  final now = DateTime.now();

  final start = DateTime(
    now.year,
    now.month,
    1,
  );

  final end = DateTime(
    now.year,
    now.month + 1,
    1,
  );

  final result = await db.rawQuery(
    '''
    SELECT COALESCE(SUM(amount), 0) AS total
    FROM transactions
    WHERE type = 'income'
    AND transaction_date >= ?
    AND transaction_date < ?
    ''',
    [
      start.toIso8601String(),
      end.toIso8601String(),
    ],
  );

  return (result.first['total'] as num)
      .toDouble();
}
Future<double> getMonthlyExpenses() async {
  final db = await AppDatabase.database;

  final now = DateTime.now();

  final start = DateTime(
    now.year,
    now.month,
    1,
  );

  final end = DateTime(
    now.year,
    now.month + 1,
    1,
  );

  final result = await db.rawQuery(
    '''
    SELECT COALESCE(SUM(amount), 0) AS total
    FROM transactions
    WHERE type = 'expense'
    AND transaction_date >= ?
    AND transaction_date < ?
    ''',
    [
      start.toIso8601String(),
      end.toIso8601String(),
    ],
  );

  return (result.first['total'] as num)
      .toDouble();
}
lib/screens/dashboard/dashboard_screen.dart
import 'package:flutter/material.dart';

import '../../services/finance_service.dart';
import '../accounts/accounts_screen.dart';
import '../movements/new_transaction_screen.dart';

class DashboardScreen extends StatefulWidget {
  const DashboardScreen({
    super.key,
  });

  @override
  State<DashboardScreen> createState() =>
      _DashboardScreenState();
}

class _DashboardScreenState
    extends State<DashboardScreen> {
  final FinanceService _service =
      FinanceService();

  double _balance = 0;
  double _income = 0;
  double _expenses = 0;

  bool _loading = true;

  @override
  void initState() {
    super.initState();
    _loadDashboard();
  }

  Future<void> _loadDashboard() async {
    setState(() {
      _loading = true;
    });

    final balance =
        await _service.getTotalBalance();

    final income =
        await _service.getMonthlyIncome();

    final expenses =
        await _service.getMonthlyExpenses();

    if (!mounted) return;

    setState(() {
      _balance = balance;
      _income = income;
      _expenses = expenses;
      _loading = false;
    });
  }

  String _money(double value) {
    return '₡${value.toStringAsFixed(2)}';
  }

  @override
  Widget build(
    BuildContext context,
  ) {
    return Scaffold(
      appBar: AppBar(
        title: const Text(
          'Finanzas Hogar Pro',
        ),
        actions: [
          IconButton(
            onPressed: _loadDashboard,
            icon: const Icon(
              Icons.refresh,
            ),
          ),
        ],
      ),

      body: RefreshIndicator(
        onRefresh: _loadDashboard,
        child: _loading
            ? const Center(
                child:
                    CircularProgressIndicator(),
              )
            : ListView(
                padding:
                    const EdgeInsets.all(16),
                children: [
                  const Text(
                    'Resumen financiero',
                    style: TextStyle(
                      fontSize: 26,
                      fontWeight:
                          FontWeight.bold,
                    ),
                  ),

                  const SizedBox(height: 20),

                  Card(
                    child: Padding(
                      padding:
                          const EdgeInsets.all(24),
                      child: Column(
                        crossAxisAlignment:
                            CrossAxisAlignment.start,
                        children: [
                          const Text(
                            'Saldo disponible',
                          ),
                          const SizedBox(
                            height: 8,
                          ),
                          Text(
                            _money(_balance),
                            style:
                                const TextStyle(
                              fontSize: 32,
                              fontWeight:
                                  FontWeight.bold,
                            ),
                          ),
                        ],
                      ),
                    ),
                  ),

                  const SizedBox(height: 16),

                  Row(
                    children: [
                      Expanded(
                        child: _SummaryCard(
                          title: 'Ingresos',
                          amount:
                              _money(_income),
                          icon:
                              Icons.arrow_downward,
                        ),
                      ),
                      const SizedBox(
                        width: 12,
                      ),
                      Expanded(
                        child: _SummaryCard(
                          title: 'Gastos',
                          amount:
                              _money(_expenses),
                          icon:
                              Icons.arrow_upward,
                        ),
                      ),
                    ],
                  ),

                  const SizedBox(height: 16),

                  Card(
                    child: ListTile(
                      leading: const CircleAvatar(
                        child: Icon(
                          Icons.savings,
                        ),
                      ),
                      title: const Text(
                        'Ahorro del mes',
                      ),
                      subtitle: Text(
                        _money(
                          _income -
                              _expenses,
                        ),
                      ),
                    ),
                  ),

                  const SizedBox(height: 24),

                  SizedBox(
                    height: 52,
                    child: FilledButton.icon(
                      onPressed: () async {
                        await Navigator.push(
                          context,
                          MaterialPageRoute(
                            builder: (_) =>
                                const NewTransactionScreen(),
                          ),
                        );

                        _loadDashboard();
                      },
                      icon: const Icon(
                        Icons.add,
                      ),
                      label: const Text(
                        'Agregar movimiento',
                      ),
                    ),
                  ),

                  const SizedBox(height: 12),

                  OutlinedButton.icon(
                    onPressed: () async {
                      await Navigator.push(
                        context,
                        MaterialPageRoute(
                          builder: (_) =>
                              const AccountsScreen(),
                        ),
                      );

                      _loadDashboard();
                    },
                    icon: const Icon(
                      Icons.account_balance_wallet,
                    ),
                    label: const Text(
                      'Mis cuentas',
                    ),
                  ),
                ],
              ),
      ),
    );
  }
}

class _SummaryCard
    extends StatelessWidget {
  final String title;
  final String amount;
  final IconData icon;

  const _SummaryCard({
    required this.title,
    required this.amount,
    required this.icon,
  });

  @override
  Widget build(
    BuildContext context,
  ) {
    return Card(
      child: Padding(
        padding:
            const EdgeInsets.all(16),
        child: Column(
          crossAxisAlignment:
              CrossAxisAlignment.start,
          children: [
            Icon(icon),
            const SizedBox(height: 8),
            Text(title),
            const SizedBox(height: 4),
            Text(
              amount,
              style:
                  const TextStyle(
                fontSize: 18,
                fontWeight:
                    FontWeight.bold,
              ),
            ),
          ],
        ),
      ),
    );
  }
}
Banco Nacional
Cuenta de ahorro
CRC
₡500.000
Ingreso
Salario
₡800.000
Gasto
Alimentación
₡75.000
Saldo disponible
₡1.225.000

Ingresos
₡800.000

Gastos
₡75.000

Ahorro
₡725.000
₡500.000
+ ₡800.000
-  ₡75.000
= ₡1.225.000
₡800.000 - ₡75.000
= ₡725.000
Banco CRC       ₡1.000.000
Banco USD          $1.000
Ahorro EUR          €500
--------------------------------
Patrimonio en CRC  ₡...
$1.000 + ₡1.000.000
lib/services/finance_service.dart
Future<int> createTransfer({
  required int sourceAccountId,
  required int destinationAccountId,
  required double amount,
  String description = '',
  DateTime? transferDate,
}) async {
  if (sourceAccountId == destinationAccountId) {
    throw Exception(
      'La cuenta de origen y destino deben ser diferentes.',
    );
  }

  if (amount <= 0) {
    throw Exception(
      'El monto debe ser mayor que cero.',
    );
  }

  final db = await AppDatabase.database;

  return db.insert(
    'transfers',
    {
      'source_account_id': sourceAccountId,
      'destination_account_id': destinationAccountId,
      'amount': amount,
      'transfer_date':
          (transferDate ?? DateTime.now())
              .toIso8601String(),
      'description': description.trim(),
    },
  );
}
lib/screens/movements/new_transfer_screen.dart
import 'package:flutter/material.dart';

import '../../models/account.dart';
import '../../services/finance_service.dart';

class NewTransferScreen extends StatefulWidget {
  const NewTransferScreen({super.key});

  @override
  State<NewTransferScreen> createState() =>
      _NewTransferScreenState();
}

class _NewTransferScreenState
    extends State<NewTransferScreen> {
  final _formKey = GlobalKey<FormState>();

  final _amountController =
      TextEditingController();

  final _descriptionController =
      TextEditingController();

  final FinanceService _service =
      FinanceService();

  List<Account> _accounts = [];

  Account? _sourceAccount;
  Account? _destinationAccount;

  bool _loading = true;
  bool _saving = false;

  @override
  void initState() {
    super.initState();
    _loadAccounts();
  }

  Future<void> _loadAccounts() async {
    final accounts =
        await _service.getAccounts();

    if (!mounted) return;

    setState(() {
      _accounts = accounts;

      if (accounts.isNotEmpty) {
        _sourceAccount = accounts.first;

        if (accounts.length > 1) {
          _destinationAccount =
              accounts[1];
        }
      }

      _loading = false;
    });
  }

  Future<void> _saveTransfer() async {
    if (!_formKey.currentState!.validate()) {
      return;
    }

    if (_sourceAccount == null ||
        _destinationAccount == null) {
      return;
    }

    if (_sourceAccount!.id ==
        _destinationAccount!.id) {
      ScaffoldMessenger.of(context)
          .showSnackBar(
        const SnackBar(
          content: Text(
            'Seleccione cuentas diferentes.',
          ),
        ),
      );
      return;
    }

    if (_sourceAccount!.currencyId !=
        _destinationAccount!.currencyId) {
      ScaffoldMessenger.of(context)
          .showSnackBar(
        const SnackBar(
          content: Text(
            'Por ahora las transferencias '
            'deben utilizar la misma moneda.',
          ),
        ),
      );
      return;
    }

    setState(() {
      _saving = true;
    });

    try {
      final amount = double.parse(
        _amountController.text
            .replaceAll('.', '')
            .replaceAll(',', '.'),
      );

      final balance =
          await _service.getAccountBalance(
        _sourceAccount!.id!,
      );

      if (amount > balance) {
        throw Exception(
          'La cuenta de origen no tiene '
          'saldo suficiente.',
        );
      }

      await _service.createTransfer(
        sourceAccountId:
            _sourceAccount!.id!,
        destinationAccountId:
            _destinationAccount!.id!,
        amount: amount,
        description:
            _descriptionController.text,
      );

      if (!mounted) return;

      Navigator.pop(context);
    } catch (e) {
      if (!mounted) return;

      ScaffoldMessenger.of(context)
          .showSnackBar(
        SnackBar(
          content: Text(
            e.toString()
                .replaceFirst(
                  'Exception: ',
                  '',
                ),
          ),
        ),
      );
    } finally {
      if (mounted) {
        setState(() {
          _saving = false;
        });
      }
    }
  }

  @override
  void dispose() {
    _amountController.dispose();
    _descriptionController.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title:
            const Text('Transferir dinero'),
      ),
      body: _loading
          ? const Center(
              child:
                  CircularProgressIndicator(),
            )
          : Form(
              key: _formKey,
              child: ListView(
                padding:
                    const EdgeInsets.all(20),
                children: [
                  DropdownButtonFormField<Account>(
                    value: _sourceAccount,
                    decoration:
                        const InputDecoration(
                      labelText: 'Cuenta origen',
                      border:
                          OutlineInputBorder(),
                    ),
                    items: _accounts
                        .map(
                          (account) =>
                              DropdownMenuItem(
                            value: account,
                            child: Text(
                              account.name,
                            ),
                          ),
                        )
                        .toList(),
                    onChanged: (value) {
                      setState(() {
                        _sourceAccount =
                            value;
                      });
                    },
                  ),

                  const SizedBox(height: 20),

                  const Icon(
                    Icons.arrow_downward,
                    size: 32,
                  ),

                  const SizedBox(height: 20),

                  DropdownButtonFormField<Account>(
                    value:
                        _destinationAccount,
                    decoration:
                        const InputDecoration(
                      labelText:
                          'Cuenta destino',
                      border:
                          OutlineInputBorder(),
                    ),
                    items: _accounts
                        .map(
                          (account) =>
                              DropdownMenuItem(
                            value: account,
                            child: Text(
                              account.name,
                            ),
                          ),
                        )
                        .toList(),
                    onChanged: (value) {
                      setState(() {
                        _destinationAccount =
                            value;
                      });
                    },
                  ),

                  const SizedBox(height: 20),

                  TextFormField(
                    controller:
                        _amountController,
                    keyboardType:
                        const TextInputType
                            .numberWithOptions(
                      decimal: true,
                    ),
                    decoration:
                        const InputDecoration(
                      labelText: 'Monto',
                      prefixText: '₡ ',
                      border:
                          OutlineInputBorder(),
                    ),
                    validator: (value) {
                      if (value == null ||
                          value.trim().isEmpty) {
                        return 'Ingrese el monto';
                      }

                      final amount =
                          double.tryParse(
                        value
                            .replaceAll(
                                '.', '')
                            .replaceAll(
                                ',', '.'),
                      );

                      if (amount == null ||
                          amount <= 0) {
                        return 'Ingrese un monto válido';
                      }

                      return null;
                    },
                  ),

                  const SizedBox(height: 20),

                  TextFormField(
                    controller:
                        _descriptionController,
                    decoration:
                        const InputDecoration(
                      labelText:
                          'Descripción',
                      hintText:
                          'Ej. Transferencia a ahorros',
                      border:
                          OutlineInputBorder(),
                    ),
                  ),

                  const SizedBox(height: 32),

                  SizedBox(
                    height: 52,
                    child: FilledButton.icon(
                      onPressed:
                          _saving
                              ? null
                              : _saveTransfer,
                      icon: const Icon(
                        Icons.swap_horiz,
                      ),
                      label: Text(
                        _saving
                            ? 'Procesando...'
                            : 'Realizar transferencia',
                      ),
                    ),
                  ),
                ],
              ),
            ),
    );
  }
}
dashboard_screen.dart
import '../movements/new_transfer_screen.dart';
const SizedBox(height: 12),

OutlinedButton.icon(
  onPressed: () async {
    await Navigator.push(
      context,
      MaterialPageRoute(
        builder: (_) =>
            const NewTransferScreen(),
      ),
    );

    _loadDashboard();
  },
  icon: const Icon(Icons.swap_horiz),
  label: const Text(
    'Transferir dinero',
  ),
),
Banco Nacional
CRC
₡500.000
Cuenta de ahorro
CRC
₡100.000
Transferir dinero

Origen:
Banco Nacional

Destino:
Cuenta de ahorro

Monto:
₡100.000
Banco Nacional       ₡400.000
Cuenta de ahorro     ₡200.000
------------------------------
Total                ₡600.000
INGRESO
    ↓
Aumenta efectivo

GASTO
    ↓
Disminuye efectivo

TRANSFERENCIA
    ↓
Mueve efectivo
    ↓
NO modifica ingresos
    ↓
NO modifica gastos
16/08/2026
Salario
+ ₡800.000

16/08/2026
Supermercado
- ₡75.000

16/08/2026
Transferencia a ahorro
↔ ₡100.000
lib/services/transaction_service.dart
import '../database/app_database.dart';

class TransactionService {
  Future<List<Map<String, dynamic>>> getTransactions({
    String? type,
    int? accountId,
  }) async {
    final db = await AppDatabase.database;

    String? where;
    List<dynamic>? whereArgs;

    final conditions = <String>[];

    if (type != null && type.isNotEmpty) {
      conditions.add('t.type = ?');
    }

    if (accountId != null) {
      conditions.add('t.account_id = ?');
    }

    if (conditions.isNotEmpty) {
      where = conditions.join(' AND ');

      whereArgs = [];

      if (type != null && type.isNotEmpty) {
        whereArgs.add(type);
      }

      if (accountId != null) {
        whereArgs.add(accountId);
      }
    }

    return db.rawQuery(
      '''
      SELECT
        t.id,
        t.account_id,
        t.category_id,
        t.type,
        t.amount,
        t.description,
        t.transaction_date,
        a.name AS account_name,
        c.name AS category_name
      FROM transactions t
      LEFT JOIN accounts a
        ON a.id = t.account_id
      LEFT JOIN categories c
        ON c.id = t.category_id
      ${where == null ? '' : 'WHERE $where'}
      ORDER BY t.transaction_date DESC
      ''',
      whereArgs,
    );
  }

  Future<void> deleteTransaction(
    int id,
  ) async {
    final db = await AppDatabase.database;

    await db.delete(
      'transactions',
      where: 'id = ?',
      whereArgs: [id],
    );
  }
}
lib/screens/movements/movements_screen.dart
import 'package:flutter/material.dart';

import '../../services/transaction_service.dart';
import 'new_transaction_screen.dart';

class MovementsScreen extends StatefulWidget {
  const MovementsScreen({
    super.key,
  });

  @override
  State<MovementsScreen> createState() =>
      _MovementsScreenState();
}

class _MovementsScreenState
    extends State<MovementsScreen> {
  final TransactionService _service =
      TransactionService();

  List<Map<String, dynamic>> _movements = [];

  String _filter = 'all';

  bool _loading = true;

  @override
  void initState() {
    super.initState();
    _loadMovements();
  }

  Future<void> _loadMovements() async {
    setState(() {
      _loading = true;
    });

    final movements =
        await _service.getTransactions(
      type: _filter == 'all'
          ? null
          : _filter,
    );

    if (!mounted) return;

    setState(() {
      _movements = movements;
      _loading = false;
    });
  }

  String _formatAmount(
    Map<String, dynamic> movement,
  ) {
    final amount =
        (movement['amount'] as num)
            .toDouble();

    if (movement['type'] == 'income') {
      return '+ ₡${amount.toStringAsFixed(2)}';
    }

    return '- ₡${amount.toStringAsFixed(2)}';
  }

  String _formatType(String type) {
    switch (type) {
      case 'income':
        return 'Ingreso';

      case 'expense':
        return 'Gasto';

      default:
        return type;
    }
  }

  IconData _getIcon(String type) {
    switch (type) {
      case 'income':
        return Icons.arrow_downward;

      case 'expense':
        return Icons.arrow_upward;

      default:
        return Icons.receipt_long;
    }
  }

  Future<void> _deleteMovement(
    int id,
  ) async {
    final confirmed =
        await showDialog<bool>(
      context: context,
      builder: (context) {
        return AlertDialog(
          title: const Text(
            'Eliminar movimiento',
          ),
          content: const Text(
            '¿Está seguro de eliminar este '
            'movimiento?',
          ),
          actions: [
            TextButton(
              onPressed: () {
                Navigator.pop(
                  context,
                  false,
                );
              },
              child: const Text(
                'Cancelar',
              ),
            ),
            FilledButton(
              onPressed: () {
                Navigator.pop(
                  context,
                  true,
                );
              },
              child: const Text(
                'Eliminar',
              ),
            ),
          ],
        );
      },
    );

    if (confirmed != true) {
      return;
    }

    await _service.deleteTransaction(id);

    _loadMovements();
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text(
          'Movimientos',
        ),
        actions: [
          IconButton(
            onPressed: _loadMovements,
            icon: const Icon(
              Icons.refresh,
            ),
          ),
        ],
      ),
      body: Column(
        children: [
          Padding(
            padding:
                const EdgeInsets.all(12),
            child: SingleChildScrollView(
              scrollDirection:
                  Axis.horizontal,
              child: Row(
                children: [
                  FilterChip(
                    label:
                        const Text('Todos'),
                    selected:
                        _filter == 'all',
                    onSelected: (_) {
                      setState(() {
                        _filter = 'all';
                      });

                      _loadMovements();
                    },
                  ),
                  const SizedBox(width: 8),
                  FilterChip(
                    label:
                        const Text('Ingresos'),
                    selected:
                        _filter == 'income',
                    onSelected: (_) {
                      setState(() {
                        _filter = 'income';
                      });

                      _loadMovements();
                    },
                  ),
                  const SizedBox(width: 8),
                  FilterChip(
                    label:
                        const Text('Gastos'),
                    selected:
                        _filter == 'expense',
                    onSelected: (_) {
                      setState(() {
                        _filter = 'expense';
                      });

                      _loadMovements();
                    },
                  ),
                ],
              ),
            ),
          ),

          Expanded(
            child: _loading
                ? const Center(
                    child:
                        CircularProgressIndicator(),
                  )
                : _movements.isEmpty
                    ? const _EmptyMovements()
                    : RefreshIndicator(
                        onRefresh:
                            _loadMovements,
                        child:
                            ListView.builder(
                          padding:
                              const EdgeInsets
                                  .all(12),
                          itemCount:
                              _movements.length,
                          itemBuilder:
                              (context, index) {
                            final movement =
                                _movements[
                                    index];

                            final type =
                                movement[
                                    'type'];

                            final description =
                                movement[
                                        'description']
                                    as String? ??
                                    'Sin descripción';

                            final category =
                                movement[
                                        'category_name']
                                    as String? ??
                                    'Sin categoría';

                            final account =
                                movement[
                                        'account_name']
                                    as String? ??
                                    'Sin cuenta';

                            final id =
                                movement['id']
                                    as int;

                            return Card(
                              margin:
                                  const EdgeInsets
                                      .only(
                                bottom: 10,
                              ),
                              child:
                                  ListTile(
                                leading:
                                    CircleAvatar(
                                  child:
                                      Icon(
                                    _getIcon(
                                      type,
                                    ),
                                  ),
                                ),
                                title: Text(
                                  description,
                                  style:
                                      const TextStyle(
                                    fontWeight:
                                        FontWeight
                                            .bold,
                                  ),
                                ),
                                subtitle:
                                    Text(
                                  '$category • '
                                  '$account',
                                ),
                                trailing:
                                    Column(
                                  mainAxisAlignment:
                                      MainAxisAlignment
                                          .center,
                                  crossAxisAlignment:
                                      CrossAxisAlignment
                                          .end,
                                  children: [
                                    Text(
                                      _formatAmount(
                                        movement,
                                      ),
                                      style:
                                          const TextStyle(
                                        fontWeight:
                                            FontWeight
                                                .bold,
                                      ),
                                    ),
                                    Text(
                                      _formatType(
                                        type,
                                      ),
                                      style:
                                          Theme.of(
                                        context,
                                      )
                                          .textTheme
                                          .bodySmall,
                                    ),
                                  ],
                                ),
                                onLongPress:
                                    () {
                                  _deleteMovement(
                                    id,
                                  );
                                },
                              ),
                            );
                          },
                        ),
                      ),
          ),
        ],
      ),
      floatingActionButton:
          FloatingActionButton.extended(
        onPressed: () async {
          await Navigator.push(
            context,
            MaterialPageRoute(
              builder: (_) =>
                  const NewTransactionScreen(),
            ),
          );

          _loadMovements();
        },
        icon: const Icon(
          Icons.add,
        ),
        label: const Text(
          'Movimiento',
        ),
      ),
    );
  }
}

class _EmptyMovements
    extends StatelessWidget {
  const _EmptyMovements();

  @override
  Widget build(
    BuildContext context,
  ) {
    return Center(
      child: Padding(
        padding:
            const EdgeInsets.all(32),
        child: Column(
          mainAxisAlignment:
              MainAxisAlignment.center,
          children: [
            const Icon(
              Icons.receipt_long_outlined,
              size: 70,
            ),
            const SizedBox(height: 20),
            const Text(
              'No hay movimientos',
              style: TextStyle(
                fontSize: 20,
                fontWeight:
                    FontWeight.bold,
              ),
            ),
            const SizedBox(height: 8),
            const Text(
              'Los ingresos y gastos que '
              'registres aparecerán aquí.',
              textAlign:
                  TextAlign.center,
            ),
          ],
        ),
      ),
    );
  }
}
lib/screens/dashboard/dashboard_screen.dart
import '../movements/movements_screen.dart';
const SizedBox(height: 12),

OutlinedButton.icon(
  onPressed: () async {
    await Navigator.push(
      context,
      MaterialPageRoute(
        builder: (_) =>
            const MovementsScreen(),
      ),
    );

    _loadDashboard();
  },
  icon: const Icon(
    Icons.receipt_long,
  ),
  label: const Text(
    'Historial de movimientos',
  ),
),
Dashboard
   │
   ├── Cuentas
   │
   ├── Ingresos
   │
   ├── Gastos
   │
   ├── Transferencias
   │
   └── Movimientos
   16/08/2026   Salario
Ingreso      +₡800.000

16/08/2026   Supermercado
Gasto        -₡75.000

16/08/2026   Banco → Ahorros
Transferencia  ₡100.000
flutter clean
flutter pub get
flutter analyze
flutter run
Alimentación       ₡250.000
Vivienda           ₡300.000
Transporte         ₡100.000
Entretenimiento     ₡50.000
ALIMENTACIÓN
Presupuesto     ₡250.000
Gastado         ₡185.000
Disponible       ₡65.000
████████████░░░
lib/models/budget.dart
class Budget {
  final int? id;
  final int categoryId;
  final double amount;
  final int month;
  final int year;

  const Budget({
    this.id,
    required this.categoryId,
    required this.amount,
    required this.month,
    required this.year,
  });

  Map<String, dynamic> toMap() {
    return {
      'id': id,
      'category_id': categoryId,
      'amount': amount,
      'month': month,
      'year': year,
    };
  }

  factory Budget.fromMap(
    Map<String, dynamic> map,
  ) {
    return Budget(
      id: map['id'] as int?,
      categoryId: map['category_id'] as int,
      amount: (map['amount'] as num).toDouble(),
      month: map['month'] as int,
      year: map['year'] as int,
    );
  }
}
lib/services/budget_service.dart
import '../database/app_database.dart';

class BudgetService {
  Future<int> createBudget({
    required int categoryId,
    required double amount,
    required int month,
    required int year,
  }) async {
    final db = await AppDatabase.database;

    return db.insert(
      'budgets',
      {
        'category_id': categoryId,
        'amount': amount,
        'month': month,
        'year': year,
      },
    );
  }

  Future<List<Map<String, dynamic>>> getBudgets({
    required int month,
    required int year,
  }) async {
    final db = await AppDatabase.database;

    return db.rawQuery(
      '''
      SELECT
        b.id,
        b.category_id,
        b.amount,
        b.month,
        b.year,
        c.name AS category_name
      FROM budgets b
      INNER JOIN categories c
        ON c.id = b.category_id
      WHERE b.month = ?
        AND b.year = ?
      ORDER BY c.name ASC
      ''',
      [month, year],
    );
  }

  Future<double> getCategorySpent({
    required int categoryId,
    required int month,
    required int year,
  }) async {
    final db = await AppDatabase.database;

    final start = DateTime(
      year,
      month,
      1,
    );

    final end = DateTime(
      year,
      month + 1,
      1,
    );

    final result = await db.rawQuery(
      '''
      SELECT COALESCE(SUM(amount), 0) AS total
      FROM transactions
      WHERE category_id = ?
        AND type = 'expense'
        AND transaction_date >= ?
        AND transaction_date < ?
      ''',
      [
        categoryId,
        start.toIso8601String(),
        end.toIso8601String(),
      ],
    );

    return (result.first['total'] as num)
        .toDouble();
  }

  Future<void> deleteBudget(
    int id,
  ) async {
    final db = await AppDatabase.database;

    await db.delete(
      'budgets',
      where: 'id = ?',
      whereArgs: [id],
    );
  }
}
lib/screens/budgets/budgets_screen.dart
import 'package:flutter/material.dart';

import '../../services/budget_service.dart';

class BudgetsScreen extends StatefulWidget {
  const BudgetsScreen({
    super.key,
  });

  @override
  State<BudgetsScreen> createState() =>
      _BudgetsScreenState();
}

class _BudgetsScreenState
    extends State<BudgetsScreen> {
  final BudgetService _service =
      BudgetService();

  List<Map<String, dynamic>> _budgets = [];

  bool _loading = true;

  late int _month;
  late int _year;

  @override
  void initState() {
    super.initState();

    final now = DateTime.now();

    _month = now.month;
    _year = now.year;

    _loadBudgets();
  }

  Future<void> _loadBudgets() async {
    setState(() {
      _loading = true;
    });

    final budgets =
        await _service.getBudgets(
      month: _month,
      year: _year,
    );

    final List<Map<String, dynamic>> result =
        [];

    for (final budget in budgets) {
      final spent =
          await _service.getCategorySpent(
        categoryId:
            budget['category_id'] as int,
        month: _month,
        year: _year,
      );

      result.add({
        ...budget,
        'spent': spent,
      });
    }

    if (!mounted) return;

    setState(() {
      _budgets = result;
      _loading = false;
    });
  }

  String _money(double value) {
    return '₡${value.toStringAsFixed(2)}';
  }

  Color _progressColor(
    double percentage,
  ) {
    if (percentage >= 1) {
      return Colors.red;
    }

    if (percentage >= .8) {
      return Colors.orange;
    }

    return Colors.green;
  }

  @override
  Widget build(
    BuildContext context,
  ) {
    return Scaffold(
      appBar: AppBar(
        title: const Text(
          'Presupuesto familiar',
        ),
      ),
      body: _loading
          ? const Center(
              child:
                  CircularProgressIndicator(),
            )
          : RefreshIndicator(
              onRefresh: _loadBudgets,
              child: _budgets.isEmpty
                  ? ListView(
                      children: const [
                        SizedBox(height: 180),
                        Center(
                          child: Text(
                            'No hay presupuestos '
                            'para este mes.',
                          ),
                        ),
                      ],
                    )
                  : ListView.builder(
                      padding:
                          const EdgeInsets.all(16),
                      itemCount:
                          _budgets.length,
                      itemBuilder:
                          (context, index) {
                        final budget =
                            _budgets[index];

                        final amount =
                            (budget['amount']
                                    as num)
                                .toDouble();

                        final spent =
                            (budget['spent']
                                    as num)
                                .toDouble();

                        final available =
                            amount - spent;

                        final percentage =
                            amount == 0
                                ? 0
                                : spent /
                                    amount;

                        final progress =
                            percentage > 1
                                ? 1.0
                                : percentage;

                        return Card(
                          margin:
                              const EdgeInsets
                                  .only(
                            bottom: 14,
                          ),
                          child: Padding(
                            padding:
                                const EdgeInsets.all(
                              18,
                            ),
                            child: Column(
                              crossAxisAlignment:
                                  CrossAxisAlignment
                                      .start,
                              children: [
                                Text(
                                  budget[
                                      'category_name'],
                                  style:
                                      const TextStyle(
                                    fontSize: 18,
                                    fontWeight:
                                        FontWeight
                                            .bold,
                                  ),
                                ),

                                const SizedBox(
                                  height: 12,
                                ),

                                Row(
                                  mainAxisAlignment:
                                      MainAxisAlignment
                                          .spaceBetween,
                                  children: [
                                    Text(
                                      'Presupuesto: '
                                      '${_money(amount)}',
                                    ),
                                    Text(
                                      'Gastado: '
                                      '${_money(spent)}',
                                    ),
                                  ],
                                ),

                                const SizedBox(
                                  height: 10,
                                ),

                                LinearProgressIndicator(
                                  value: progress,
                                  color:
                                      _progressColor(
                                    percentage,
                                  ),
                                ),

                                const SizedBox(
                                  height: 10,
                                ),

                                Text(
                                  available >= 0
                                      ? 'Disponible: '
                                        '${_money(available)}'
                                      : 'Excedido por: '
                                        '${_money(available.abs())}',
                                  style:
                                      TextStyle(
                                    fontWeight:
                                        FontWeight
                                            .bold,
                                    color:
                                        _progressColor(
                                      percentage,
                                    ),
                                  ),
                                ),

                                if (percentage >=
                                    1)
                                  const Padding(
                                    padding:
                                        EdgeInsets.only(
                                      top: 8,
                                    ),
                                    child: Text(
                                      '⚠️ Presupuesto excedido',
                                      style:
                                          TextStyle(
                                        fontWeight:
                                            FontWeight
                                                .bold,
                                      ),
                                    ),
                                  )
                                else if (percentage >=
                                    .8)
                                  const Padding(
                                    padding:
                                        EdgeInsets.only(
                                      top: 8,
                                    ),
                                    child: Text(
                                      '⚠️ Ha utilizado el 80% o más',
                                    ),
                                  ),
                              ],
                            ),
                          ),
                        );
                      },
                    ),
            ),
    );
  }
}
dashboard_screen.dart
import '../budgets/budgets_screen.dart';
const SizedBox(height: 12),

OutlinedButton.icon(
  onPressed: () async {
    await Navigator.push(
      context,
      MaterialPageRoute(
        builder: (_) =>
            const BudgetsScreen(),
      ),
    );

    _loadDashboard();
  },
  icon: const Icon(
    Icons.pie_chart_outline,
  ),
  label: const Text(
    'Presupuesto familiar',
  ),
),
budgets
lib/screens/budgets/new_budget_screen.dart
Categoría
[ Alimentación          ▼ ]

Mes
[ Agosto 2026           ]

Presupuesto
[ ₡250.000                ]

             [ GUARDAR ]
             ALIMENTACIÓN

Presupuesto     ₡250.000
Gastado         ₡185.000
Disponible       ₡65.000

████████████░░░░
accounts
categories
transactions
transfers
budgets
debts
assets
goals
currencies
74%
Antes:
Saldo ₡1.225.000

Eliminar gasto:
+ ₡75.000

Después:
Saldo ₡1.300.000

Banco       -₡100.000
Ahorros     +₡100.000
Banco       +₡100.000
Ahorros     -₡100.000
flutter clean
flutter pub get
flutter analyze
flutter run

EUR
...
