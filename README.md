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
currencies
-----------
CRC
USD
EUR
...
