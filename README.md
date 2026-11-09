# Між нами — комплект PWA

Це перша робоча версія встановлюваного вебзастосунку для двох. Є щоденні завдання (7 наборів, що циклічно змінюються), відмітки виконання, імена, код пари, офлайн-кеш і опційна синхронізація через Supabase.

## Як запустити локально

Потрібен будь-який статичний хостинг із HTTPS (наприклад, GitHub Pages, Cloudflare Pages або Netlify). Завантажте вміст цієї папки в репозиторій/проєкт і опублікуйте. Відкрийте адресу в Safari на iPhone → кнопка «Поділитися» → «На початковий екран».

Просто відкрити index.html як файл недостатньо для встановлення PWA: потрібен HTTPS-хостинг.

## Синхронізація двох телефонів (безкоштовний Supabase)

1. Створіть проєкт у Supabase.
2. У SQL Editor виконайте:

```sql
create table if not exists public.couples (
  room_code text primary key,
  data jsonb not null,
  updated_at timestamptz not null default now()
);
alter table public.couples enable row level security;
create policy "public read couples" on public.couples for select to anon using (true);
create policy "public insert couples" on public.couples for insert to anon with check (true);
create policy "public update couples" on public.couples for update to anon using (true) with check (true);
grant select, insert, update on public.couples to anon;
```

3. У Supabase → Project Settings → API скопіюйте Project URL та anon/public key.
4. У застосунку відкрийте ⚙ → вставте URL, anon key та код пари → «Зберегти та під’єднати».
5. На другому телефоні відкрийте той самий сайт, введіть свої імена та в налаштуваннях вставте ті самі URL, ключ і код пари.

**Безпека:** цей стартовий SQL відкриває читання/запис рядків для анонімних клієнтів. Використовуйте унікальний код пари, не зберігайте приватні секрети, паролі чи чутливі дані. Для публічного запуску потрібна авторизація та політики доступу, обмежені учасниками конкретної пари. Ключ `anon/public` можна використовувати у клієнті; `service_role` — ніколи.

## Важливі обмеження першої версії

- Дані локально зберігаються на пристрої; синхронізація працює після налаштування Supabase.
- Синхронізація перевіряється приблизно кожні 7 секунд; це базова модель спільного стану, не повноцінний чат у реальному часі.
- Нативний віджет iOS неможливо додати до PWA. Для нього потрібен окремий застосунок із WidgetKit та підписанням Apple.
- Щоб опублікувати застосунок як нативний iOS app, потрібен Mac/Xcode або сервіс збірки та Apple Developer signing.
