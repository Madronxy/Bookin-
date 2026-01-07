# N8N WhatsApp Booking Integration - Complete Setup Guide

## Overview
This guide provides the complete setup for integrating N8N with WhatsApp to create an automated booking system for BOOKin. When users scan the QR code, they're directed to WhatsApp where an N8N agent processes their booking requests and updates the dashboard in real-time.

## Architecture Flow
```
QR Code Scan → WhatsApp Chat → N8N Agent → Webhook → BOOKin Dashboard (Real-time)
```

## 1. BOOKin Platform Setup

### Webhook Endpoint
Your BOOKin application now includes a dedicated webhook endpoint:
```
POST https://your-replit-domain.replit.app/api/webhooks/n8n/whatsapp-booking
```

### Required JSON Payload Format
```json
{
  "client_name": "John Doe",
  "client_phone": "+27611234567", 
  "client_email": "john@example.com",
  "service_name": "Hair Cut",
  "booking_date": "2025-01-25",
  "booking_time": "14:30",
  "notes": "Requested by client",
  "source": "whatsapp",
  "business_id": "your-business-uuid-here"
}
```

### Business Configuration
In your BOOKin dashboard, configure:
1. **WhatsApp Number**: Business WhatsApp number
2. **N8N Webhook URL**: For bi-directional communication
3. **Business ID**: Copy this for N8N workflow configuration

## 2. N8N Workflow Setup

### Prerequisites
- N8N instance (cloud or self-hosted)
- WhatsApp Business API access
- OpenAI API key for conversation processing

### Workflow Components

#### Step 1: WhatsApp Trigger
```yaml
Node Type: WhatsApp Business
Configuration:
  - Webhook URL: Set in WhatsApp Business API
  - Events: message.received
  - Phone Number: Your business WhatsApp number
```

#### Step 2: Message Processing with AI
```yaml
Node Type: OpenAI
Configuration:
  Model: gpt-4
  System Prompt: |
    You are a booking assistant for [BUSINESS_NAME]. 
    Extract booking information from customer messages.
    
    Required information:
    - Customer name
    - Phone number  
    - Service requested
    - Preferred date (YYYY-MM-DD format)
    - Preferred time (HH:MM format)
    
    Response format (JSON only):
    {
      "client_name": "string",
      "client_phone": "string", 
      "client_email": "string|null",
      "service_name": "string",
      "booking_date": "YYYY-MM-DD",
      "booking_time": "HH:MM",
      "notes": "string",
      "is_complete": boolean,
      "missing_info": ["field1", "field2"],
      "response_message": "string"
    }
    
    If information is missing, ask for it politely.
    Always be professional and helpful.
```

#### Step 3: Information Validation
```yaml
Node Type: Function
Code: |
  const data = $json.choices[0].message.content;
  const booking = JSON.parse(data);
  
  if (booking.is_complete) {
    return [booking, null];
  } else {
    return [null, booking];
  }
```

#### Step 4A: Send to BOOKin (Complete Booking)
```yaml
Node Type: HTTP Request
Method: POST
URL: https://your-replit-domain.replit.app/api/webhooks/n8n/whatsapp-booking
Headers:
  Content-Type: application/json
Body:
{
  "client_name": "{{ $json.client_name }}",
  "client_phone": "{{ $json.client_phone }}",
  "client_email": "{{ $json.client_email }}",
  "service_name": "{{ $json.service_name }}",
  "booking_date": "{{ $json.booking_date }}",
  "booking_time": "{{ $json.booking_time }}",
  "notes": "{{ $json.notes }}",
  "source": "whatsapp",
  "business_id": "YOUR_BUSINESS_ID_HERE"
}
```

#### Step 4B: Request Missing Info (Incomplete Booking)
```yaml
Node Type: WhatsApp Business  
Configuration:
  Action: Send Message
  To: {{ $node["WhatsApp Business"].json.from }}
  Message: "{{ $json.response_message }}"
```

#### Step 5: Confirmation Message
```yaml
Node Type: WhatsApp Business
Configuration:
  Action: Send Message  
  To: {{ $node["WhatsApp Business"].json.from }}
  Message: |
    ✅ Booking confirmed!
    
    📅 Service: {{ $json.service_name }}
    📞 Date: {{ $json.booking_date }}
    🕐 Time: {{ $json.booking_time }}
    
    We'll contact you soon to confirm details.
    Thank you for choosing [BUSINESS_NAME]!
```

## 3. WhatsApp Business API Setup

### Required Configuration
1. **Meta Business Account**: Create and verify
2. **WhatsApp Business Account**: Connect to Meta Business
3. **Phone Number**: Verify business phone number
4. **Webhook Setup**: 
   ```
   Webhook URL: https://your-n8n-domain.com/webhook/whatsapp
   Verify Token: your-secure-token
   Subscription Fields: messages
   ```

### Environment Variables for N8N
```env
WHATSAPP_ACCESS_TOKEN=your_whatsapp_access_token
WHATSAPP_PHONE_NUMBER_ID=your_phone_number_id
OPENAI_API_KEY=your_openai_api_key
BOOKIN_WEBHOOK_URL=https://your-replit-domain.replit.app/api/webhooks/n8n/whatsapp-booking
BUSINESS_ID=your-business-uuid
```

## 4. QR Code Configuration

### WhatsApp QR Code Generation
The QR code should generate a WhatsApp chat link:
```
https://wa.me/27611234567?text=Hi! I'd like to make a booking for [SERVICE_NAME]. My name is [NAME] and I'm available on [DATE] at [TIME].
```

### QR Code Placement
- Print and display at business location
- Include on business cards
- Add to marketing materials
- Display on website

## 5. Testing the Integration

### Test Scenario
1. **Scan QR Code**: Should open WhatsApp with pre-filled message
2. **Send Message**: N8N should respond and ask for missing info
3. **Provide Details**: Agent should extract and confirm booking
4. **Check Dashboard**: New booking should appear in real-time

### Test Payload
```bash
curl -X POST https://your-replit-domain.replit.app/api/webhooks/n8n/whatsapp-booking \
  -H "Content-Type: application/json" \
  -d '{
    "client_name": "Test User",
    "client_phone": "+27611234567",
    "client_email": "test@example.com", 
    "service_name": "Hair Cut",
    "booking_date": "2025-01-25",
    "booking_time": "14:30",
    "notes": "Test booking from N8N",
    "source": "whatsapp",
    "business_id": "your-business-id-here"
  }'
```

## 6. Advanced Features

### Multi-Language Support
Add language detection in N8N:
```yaml
Node Type: Function
Code: |
  const message = $json.text.body;
  const language = detectLanguage(message); // Implement detection
  return { language, message };
```

### Business Hours Validation
```yaml
Node Type: Function  
Code: |
  const requestedTime = new Date(`${$json.booking_date}T${$json.booking_time}`);
  const businessHours = {
    start: "09:00",
    end: "17:00", 
    closed: ["Sunday"]
  };
  
  const isValidTime = validateBusinessHours(requestedTime, businessHours);
  return { isValidTime, suggestedTimes: [...] };
```

### Service Availability Check
```yaml
Node Type: HTTP Request
Method: GET
URL: https://your-replit-domain.replit.app/api/business/{{ env.BUSINESS_ID }}/services
Purpose: Validate requested service exists
```

## 7. Monitoring and Analytics

### N8N Workflow Metrics
- Monitor execution success rate
- Track response times
- Log failed bookings for review

### BOOKin Dashboard Metrics  
- WhatsApp booking conversion rate
- Most requested services via WhatsApp
- Peak booking times

## 8. Security Considerations

### Webhook Security
- Implement signature verification
- Use HTTPS only
- Rate limiting on webhook endpoint

### Data Privacy
- Store minimal customer data
- Implement data retention policies
- GDPR/POPIA compliance for South African businesses

## 9. Troubleshooting

### Common Issues
1. **Webhook not triggered**: Check N8N workflow activation
2. **Missing business_id**: Ensure correct business UUID in payload
3. **Invalid date format**: Use YYYY-MM-DD format
4. **WhatsApp delivery failures**: Verify phone number format

### Debug Endpoints
```bash
# Test webhook connectivity
curl https://your-replit-domain.replit.app/api/webhooks/n8n/whatsapp-booking

# Check business configuration
curl https://your-replit-domain.replit.app/api/business/your-business-id
```

## 10. Deployment Checklist

- [ ] N8N workflow created and activated
- [ ] WhatsApp Business API configured
- [ ] Webhook endpoints tested
- [ ] QR codes generated and deployed
- [ ] Business settings configured in BOOKin
- [ ] Test booking completed successfully
- [ ] Real-time dashboard updates confirmed
- [ ] Staff trained on new booking notifications

## Support
For technical support with this integration, contact the BOOKin development team or check the API documentation.