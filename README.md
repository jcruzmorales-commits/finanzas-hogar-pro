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
