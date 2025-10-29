# AnonThreads - Privacy-First Anonymous Discussion App

AnonThreads is a cross-platform anonymous discussion app built with Flutter and Supabase that prioritizes user privacy and security. Users can create threads, reply, and engage in discussions without requiring any personal information or accounts.

## 🔒 Privacy-First Design

- **No Login Required**: Post and browse anonymously without creating accounts
- **Optional End-to-End Encryption**: Create private posts that only you can read
- **Secure Token Storage**: Owner tokens stored securely with optional biometric protection
- **Anonymous Reactions**: Like and react without revealing identity
- **No Personal Data Collection**: No emails, phone numbers, or personal information required

## ✨ Key Features

### Core Functionality
- **Thread Creation**: Create text posts with optional images and links
- **Nested Replies**: Support for threaded conversations
- **Anonymous Reactions**: Like posts and replies anonymously
- **Real-time Feed**: Live updates using Supabase subscriptions
- **Public Feed**: Browse all public posts without authentication

### Privacy & Security
- **Private Posts**: End-to-end encrypted posts using AES-GCM
- **Owner Tokens**: Secure device-based authentication for private content
- **Biometric Protection**: Optional biometric unlock for secure storage
- **Secure Storage**: Flutter Secure Storage for sensitive data
- **Content Moderation**: Profanity filtering and abuse reporting

### Technical Features
- **Cross-Platform**: Works on iOS, Android, Web, and Desktop
- **Offline-First**: Local data caching and sync
- **Real-time Updates**: Live feed updates via Supabase subscriptions
- **Responsive UI**: Material Design 3 with adaptive layouts

## 🏗️ Architecture

### Tech Stack
- **Frontend**: Flutter (Dart)
- **Backend**: Supabase (PostgreSQL + Auth + Real-time)
- **Encryption**: AES-GCM for client-side encryption
- **Storage**: Flutter Secure Storage with biometric support
- **State Management**: Provider pattern

### Project Structure
```
lib/
├── config/
│   └── supabase_config.dart      # Supabase configuration
├── models/
│   ├── post.dart                 # Post data model
│   ├── reply.dart                # Reply data model
│   └── reaction.dart             # Reaction data model
├── services/
│   ├── supabase_service.dart     # Supabase client wrapper
│   ├── encryption_service.dart   # Client-side encryption
│   ├── secure_storage_service.dart # Secure token storage
│   ├── owner_token_service.dart  # Owner token management
│   ├── post_service.dart         # Post CRUD operations
│   ├── reply_service.dart        # Reply management
│   ├── reaction_service.dart     # Reaction handling
│   ├── realtime_service.dart     # Real-time subscriptions
│   └── biometric_auth_service.dart # Biometric authentication
├── providers/
│   └── app_provider.dart         # Global state management
├── screens/
│   ├── home_screen.dart          # Main feed screen
│   ├── create_post_screen.dart   # Post creation screen
│   └── my_posts_screen.dart      # Private posts screen
└── widgets/
    ├── post_card.dart            # Post display widget
    ├── loading_widget.dart       # Loading indicator
    └── error_widget.dart         # Error display
```

## 🚀 Getting Started

### Prerequisites
- Flutter SDK (3.9.2 or later)
- Supabase account and project
- Android Studio / Xcode (for mobile development)

### Installation

1. **Clone the repository**
   ```bash
   git clone <your-repo-url>
   cd anonthreads
   ```

2. **Install dependencies**
   ```bash
   flutter pub get
   ```

3. **Configure Supabase**
   - Create a new Supabase project
   - Update `lib/config/supabase_config.dart` with your credentials:
   ```dart
   static const String supabaseUrl = 'YOUR_SUPABASE_URL';
   static const String supabaseAnonKey = 'YOUR_SUPABASE_ANON_KEY';
   ```

4. **Set up the database**
   - Run the SQL scripts in `supabase_schema.sql` to create tables
   - Run the RLS policies in `supabase_rls_policies.sql`
   - Deploy the Edge Functions in `supabase/functions/`

5. **Run the app**
   ```bash
   flutter run
   ```

## 📊 Database Schema

### Tables

#### Posts Table
```sql
CREATE TABLE posts (
    id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    content TEXT NOT NULL,
    image_url TEXT,
    link_url TEXT,
    owner_token_hash TEXT,
    is_encrypted BOOLEAN DEFAULT FALSE,
    created_at TIMESTAMPTZ DEFAULT NOW(),
    updated_at TIMESTAMPTZ DEFAULT NOW()
);
```

#### Replies Table
```sql
CREATE TABLE replies (
    id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    post_id UUID NOT NULL REFERENCES posts(id) ON DELETE CASCADE,
    parent_reply_id UUID REFERENCES replies(id) ON DELETE CASCADE,
    content TEXT NOT NULL,
    owner_token_hash TEXT,
    is_encrypted BOOLEAN DEFAULT FALSE,
    created_at TIMESTAMPTZ DEFAULT NOW(),
    updated_at TIMESTAMPTZ DEFAULT NOW()
);
```

#### Reactions Table
```sql
CREATE TABLE reactions (
    id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    post_id UUID NOT NULL REFERENCES posts(id) ON DELETE CASCADE,
    reply_id UUID REFERENCES replies(id) ON DELETE CASCADE,
    reaction_type TEXT DEFAULT 'like',
    device_hash TEXT,
    created_at TIMESTAMPTZ DEFAULT NOW()
);
```

## 🔐 Security Implementation

### Owner Token System
- **Generation**: Cryptographically secure random tokens
- **Storage**: Flutter Secure Storage with optional biometric protection
- **Usage**: Hash-based authentication for private posts access

### Encryption
- **Algorithm**: AES-GCM with 256-bit keys
- **Key Storage**: Secure storage tied to device
- **Content Protection**: Client-side encryption/decryption

### Row Level Security (RLS)
- **Anonymous Access**: Public posts readable by all users
- **Private Posts**: Only accessible with correct owner token hash
- **Context Setting**: Session-based token validation

## 🔧 Supabase Edge Functions

### Content Moderation Function
- **Purpose**: Filter inappropriate content before posting
- **Features**: Profanity detection, spam prevention
- **Location**: `supabase/functions/content_moderation/`

### Report Content Function
- **Purpose**: Allow users to report abusive content
- **Features**: Anonymous reporting, admin review workflow
- **Location**: `supabase/functions/report_content/`

## 📱 User Guide

### Creating Posts
1. Tap the "+" button to create a new post
2. Choose between public or private post
3. Add content, images, or links
4. Private posts require owner token setup

### Accessing Private Posts
1. Generate an owner token in "My Posts" screen
2. Enable biometric protection (optional)
3. Private posts are automatically decrypted on your device

### Security Considerations
- **Device Security**: Private posts are lost if device is reset
- **Token Backup**: Consider backing up recovery information
- **Biometric Setup**: Enable for enhanced security

## 🔄 Real-time Features

- **Live Feed**: Posts appear instantly for all users
- **Real-time Reactions**: Like counts update immediately
- **Thread Updates**: New replies appear without refresh

## 🚨 Important Notes

### Privacy Warnings
- Private posts are only accessible on the original device
- Device theft may result in loss of private content
- Consider enabling biometric authentication for better security

### Content Moderation
- All posts are filtered for inappropriate content
- Users can report abusive content
- Moderation is handled through Supabase functions

### Performance
- Real-time subscriptions may increase battery usage
- Large threads may impact performance
- Consider pagination for better UX

## 🛠️ Development

### Running Tests
```bash
flutter test
```

### Building for Production
```bash
flutter build apk --release  # Android
flutter build ios --release  # iOS
```

### Code Quality
- Follow Flutter best practices
- Implement proper error handling
- Use type safety throughout

## 📄 License

This project is licensed under the MIT License - see the LICENSE file for details.

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Add tests if applicable
5. Submit a pull request

## 📞 Support

For support and questions:
- Create an issue in the repository
- Check the documentation
- Review the code comments for implementation details

---

**Built with ❤️ for privacy-conscious users**
#   A n o n T h r e a d s  
 