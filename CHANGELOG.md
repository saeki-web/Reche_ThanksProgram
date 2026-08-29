# Changelog - Admin Panel Update

## Version 2.0.0 (2026-08-29)

### ✨ New Features

#### **Admin Panel Interface**
- 🎨 New admin button (bottom-left, brown color #A8752E)
- 📊 Complete inquiry management dashboard
- 📈 Real-time statistics display (Total, Pending, Completed)
- 📋 Sortable inquiry table with detailed information
- 🔄 Status toggle functionality (未対応 ↔ 対応済み)

#### **Data Management**
- 💾 LocalStorage integration for inquiry persistence
- 📱 Automatic inquiry data saving to browser storage
- 🔐 JSONL-compatible storage format for server integration
- ✅ Status tracking per inquiry

#### **User Experience**
- ⌨️ Keyboard shortcut: **Shift + A** to open/close admin panel
- 🎯 One-click status updates in the admin table
- 📊 Visual status indicators (color-coded badges)
- 🔄 Real-time table refresh on status changes

### 📊 Admin Panel Features

#### Statistics Section
```
┌─────────────────────┐
│  総件数: 5          │  
│  未対応: 3          │  
│  対応済み: 2        │  
└─────────────────────┘
```

#### Inquiry Table Columns
- **日時** - Formatted timestamp (YYYY/MM/DD HH:MM)
- **カテゴリ** - Service category
- **氏名** - Customer name
- **連絡先** - Email/Phone
- **内容** - Inquiry summary (truncated with ellipsis)
- **状況** - Status button (clickable toggle)

### 🔧 Technical Updates

#### New LocalStorage Functions
```javascript
getInquiriesFromStorage()     // Read inquiries
saveInquiriesToStorage()      // Save inquiries
addInquiryToStorage()         // Add new inquiry
updateInquiryStatus()         // Update status
```

#### Updated sendInquiry() Function
- Now automatically adds records to localStorage
- Maintains backward compatibility with INQUIRY_ENDPOINT
- Console feedback when endpoint is not configured

#### New Event Listeners
- Admin button click handler
- Keyboard shortcut (Shift + A)
- Status button click delegation
- Dynamic table re-rendering

### 🎨 Styling

#### New CSS Classes
- `.tdcw-admin-btn` - Admin button
- `.tdcw-admin-panel` - Admin panel container
- `.tdcw-admin-head` - Panel header
- `.tdcw-admin-stats` - Statistics section
- `.tdcw-admin-table` - Inquiry table
- `.tdcw-admin-status` - Status buttons
- `.tdcw-admin-status.pending` - Pending badge
- `.tdcw-admin-status.done` - Completed badge

#### Colors
- Admin button: `#A8752E` (brown)
- Admin header: `#A8752E` (same as button)
- Pending status: `#FFE5CC` (light orange)
- Completed status: `#D4EDDA` (light green)

### 📦 File Changes

#### Modified: `tdc-ai-chat-widget.js`
- **Size**: ~33KB (13 KB before)
- **Added**: 200+ lines
- **Modified**: sendInquiry(), HTML, CSS
- **Backward compatible**: Yes

### ✅ Testing

All features tested with Playwright:
- ✓ Admin button appears in correct position
- ✓ Admin panel opens/closes smoothly
- ✓ Shift + A keyboard shortcut works
- ✓ Inquiries stored in localStorage
- ✓ Admin table displays inquiries
- ✓ Status toggle updates correctly
- ✓ Statistics calculate accurately
- ✓ Table re-renders on status change

### 📋 Data Persistence

#### localStorage Structure
```javascript
{
  "tdcw-inquiries": "[{
    \"id\": \"mtekuq3fxtl9r\",
    \"ts\": \"2026/08/29 16:10\",
    \"category\": \"虫歯・歯周病治療\",
    \"name\": \"テスト太郎\",
    \"contact\": \"test@example.com\",
    \"summary\": \"虫歯・歯周病治療のご予約希望\",
    \"status\": \"未対応\"
  }]"
}
```

### 🔄 Backward Compatibility

- ✅ Existing chat functionality unchanged
- ✅ CONFIG object structure unchanged
- ✅ INQUIRY_ENDPOINT still supported
- ✅ RESERVE_URL still functional
- ✅ CSS prefixing (tdcw-) maintained
- ✅ No breaking changes

### 🚀 Deployment

No additional dependencies required:
- Pure vanilla JavaScript
- No external libraries
- Single file deployment
- Works on all modern browsers

### 📝 Documentation

New documentation files:
- `ADMIN_GUIDE.md` - Admin panel user guide
- `IMPLEMENTATION.md` - WordPress integration guide
- `CHANGELOG.md` - This file

### 🐛 Known Issues

None. All features tested and working.

### 📌 Future Enhancements

- [ ] CSV/Excel export
- [ ] Advanced filtering and sorting
- [ ] Email notification integration
- [ ] Multi-user admin accounts
- [ ] Data analytics dashboard
- [ ] Automatic backup
- [ ] Search functionality
- [ ] Inquiry tagging system

### 👥 Contributors

- Initial development of admin features

### 📄 License

Same as original widget (if applicable)
