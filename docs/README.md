# Vital Connect React Native App

A comprehensive React Native application for connecting health devices, viewing health data, and sharing health information with trusted partners using the Vital SDK.

## Overview

This app provides a complete solution for health data integration, featuring:

- **Device Connection**: Connect to Apple HealthKit, Google Health Connect, and various wearables via OAuth
- **Health Data Display**: View steps, sleep, heart rate, and workout data with beautiful charts
- **Data Sharing**: Share health data with trusted partners through secure APIs
- **Deep Linking**: OAuth callback handling for seamless device connections
- **Cross-platform**: Full iOS and Android support

## Prerequisites

### Required Software
- Node.js >= 18
- React Native development environment ([setup guide](https://reactnative.dev/docs/environment-setup))
- iOS: Xcode 12+ (for iOS development)
- Android: Android Studio (for Android development)

### Vital Account
You need a Vital account to use this app:
1. Sign up at [tryvital.io](https://tryvital.io)
2. Get your API credentials from the dashboard
3. Choose your environment (sandbox for development, production for live)

## Quick Start

1. **Clone and Install**
   ```bash
   git clone <repository-url>
   cd vital-connect-rn
   yarn install
   ```

2. **Environment Setup**
   Create a `.env` file in the root directory:
   ```
   VITAL_API_KEY=your_api_key_here
   VITAL_ENVIRONMENT=sandbox
   VITAL_REGION=us
   ```

3. **Configure App Settings**
   Update `src/lib/config.tsx` with your branding:
   ```typescript
   export const AppConfig: AppConfigProps = {
     name: 'Your App Name',
     slug: 'yourapp',
     supportEmail: 'support@yourcompany.com',
     environment: 'sandbox', // or 'production'
     region: 'us', // or 'eu'
     // ... other settings
   };
   ```

4. **Run the App**
   ```bash
   # iOS
   yarn ios
   
   # Android  
   yarn android
   ```

## Architecture

### Project Structure
```
src/
├── components/          # Reusable UI components
│   ├── Card/           # Health data display cards
│   └── Text/           # Typography components
├── features/           # Feature-based organization
│   ├── ConnectDevices/ # Device connection flow
│   ├── HealthData/     # Health metrics display
│   └── ShareScreen/    # Data sharing interface
├── lib/               # Core utilities and configuration
│   ├── client/        # API client for Vital services
│   ├── config.tsx     # App configuration and theming
│   └── vitalSdk.ts    # Vital SDK integration utilities
├── navigation/        # React Navigation setup
└── hooks/            # Custom React hooks
```

### Key Technologies

- **React Native 0.80.0** - Cross-platform mobile development
- **TypeScript** - Type safety and better developer experience
- **Vital SDK** - Health data integration (@tryvital/vital-core-react-native, @tryvital/vital-health-react-native)
- **React Navigation** - Navigation and deep linking
- **GlueStack UI** - Component library
- **TanStack Query** - Data fetching and caching
- **React Native Config** - Environment variable management

## Configuration

### App Branding

Customize `src/lib/config.tsx` to match your brand:

```typescript
export const AppConfig: AppConfigProps = {
  name: 'Your Health App',
  slug: 'yourhealthapp',
  supportEmail: 'support@yourcompany.com',
  termsUrl: 'https://yourcompany.com/terms',
  privacyUrl: 'https://yourcompany.com/privacy',
  
  // Theme customization
  colors: {
    DefaultTheme: {
      background: '#F7F5F3',
      text: '#1F2937',
      'purple.400': '#7487F6',
      // ... other colors
    },
    DarkTheme: {
      background: '#101010',
      text: '#FFFFFF',
      'purple.400': '#CBD5F7',
      // ... other colors
    }
  },
  
  // Typography
  fonts: {
    regular: 'Your-Font-Regular',
    medium: 'Your-Font-Medium',
    bold: 'Your-Font-Bold',
    light: 'Your-Font-Light',
  }
};
```

### Deep Linking

Configure deep linking in `App.tsx`:

```typescript
const linking = {
  prefixes: [`${AppConfig.slug}://`],
  config: {
    screens: {
      ConnectionCallback: 'link',
    },
  },
};
```

### Health SDK Configuration

Enable/disable platform-specific health integrations:

```typescript
export const AppConfig = {
  enableHealthConnect: true,  // Android Health Connect
  enableHealthKit: true,      // iOS HealthKit
  // ... other settings
};
```

## Key Features

### 1. Device Connection (`src/features/ConnectDevices/`)

The device connection flow handles:
- OAuth-based device connections (Fitbit, Garmin, etc.)
- Native health SDK integration (HealthKit, Health Connect)
- Provider discovery and management
- Connection status tracking

**Key Components:**
- `LinkScreen.tsx` - Main device connection interface
- `ConnectedDevices.tsx` - Connected devices management
- `CallbackScreen.tsx` - OAuth callback handling

### 2. Health Data Display (`src/features/HealthData/`)

Displays health metrics with:
- Real-time data fetching
- Provider selection dropdown
- Beautiful data visualizations
- Error handling and loading states

**Data Cards:**
- `ActivityCard.tsx` - Steps and activity data
- `SleepCard.tsx` - Sleep tracking data
- `HeartRateCard.tsx` - Heart rate metrics
- `WorkoutsCard.tsx` - Workout summaries

### 3. Data Sharing (`src/features/ShareScreen/`)

Secure data sharing with:
- Partner connection management
- Share code generation
- Connection status monitoring
- Easy disconnect functionality

### 4. API Client (`src/lib/client/`)

Centralized API communication:
- RESTful API integration
- Authentication handling
- Error management
- Environment-based configuration

## API Integration

### Vital SDK Setup

The app uses Vital SDK for health data access:

```typescript
import { VitalHealth, VitalResource } from '@tryvital/vital-health-react-native';

// Configure health SDK
await VitalHealth.configure({
  logsEnabled: true,
  numberOfDaysToBackFill: 30,
  androidConfig: { syncOnAppStart: true },
  iOSConfig: {
    dataPushMode: 'automatic',
    backgroundDeliveryEnabled: true,
  },
});

// Request permissions
await VitalHealth.askForResources([
  VitalResource.Steps,
  VitalResource.Activity,
  VitalResource.HeartRate,
  VitalResource.Sleep,
  VitalResource.Workout,
]);
```

### Data Fetching

Uses TanStack Query for efficient data management:

```typescript
const { data: healthData } = useQuery({
  queryKey: ['healthData', userId, provider],
  queryFn: () => Client.Data.getTimeseries(
    userId, 'steps', startDate, endDate, provider
  ),
  refetchInterval: 5 * 60 * 1000, // 5 minutes
});
```

## Customization Guide

### Adding New Health Metrics

1. Create a new card component in `src/components/Card/`:
```typescript
export const CustomMetricCard = ({ userId, provider }) => {
  const { data } = useQuery({
    queryKey: ['customMetric', userId, provider],
    queryFn: () => Client.Data.getTimeseries(
      userId, 'custom_metric', startDate, endDate, provider
    ),
  });

  return (
    <Card>
      <CardContent>
        {/* Your custom UI */}
      </CardContent>
    </Card>
  );
};
```

2. Add to the health data screen in `src/features/HealthData/screens/index.tsx`

### Custom Styling

The app uses GlueStack UI with custom theming. Update `src/lib/config.tsx` to customize colors, fonts, and spacing.

### Adding New Device Types

1. Update the providers list in `src/lib/client/index.tsx`
2. Add new provider handling in `LinkScreen.tsx`
3. Update the device filtering logic in `src/lib/utils.tsx`

## Deployment

### iOS Deployment

1. Update bundle identifier in `ios/vitalReactNativeApp/Info.plist`
2. Configure signing in Xcode
3. Update app icons and splash screens
4. Archive and upload to App Store

### Android Deployment

1. Update package name in `android/app/build.gradle`
2. Configure signing in `android/app/build.gradle`
3. Update app icons and splash screens
4. Generate release APK/AAB

### Environment Variables

For production deployment, move sensitive configuration out of the client and handle API calls server-side:

```typescript
// Instead of direct API calls, use your backend
const response = await fetch('/api/vital/providers', {
  headers: {
    'Authorization': `Bearer ${userToken}`
  }
});
```

## Troubleshooting

### Common Issues

1. **Metro bundler issues**: Clear cache with `yarn start --reset-cache`
2. **iOS build failures**: Clean build folder `cd ios && rm -rf build && cd ..`
3. **Android build failures**: Clean gradle `cd android && ./gradlew clean && cd ..`

### Debug Mode

Enable debug logging in `src/lib/config.tsx`:
```typescript
await VitalHealth.configure({
  logsEnabled: true, // Enable for debugging
  // ... other config
});
```

## Contributing

1. Follow the existing code style and TypeScript patterns
2. Add tests for new features
3. Update documentation for API changes
4. Ensure cross-platform compatibility

## License

This project is licensed under the MIT License - see the LICENSE file for details.

## Support

For issues related to:
- Vital SDK: Contact [Vital Support](mailto:support@tryvital.io)
- React Native: Check [React Native Documentation](https://reactnative.dev/docs/getting-started)
- This codebase: Create an issue in the repository

---

This codebase provides a solid foundation for building health and fitness applications. The modular architecture makes it easy to customize for different use cases while maintaining code quality and best practices.
