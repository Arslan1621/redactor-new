# Document Redaction Tool

A secure, user-friendly web application for automatically detecting and redacting sensitive personal information (PII) from documents.

## Features

### 🔍 **Intelligent PII Detection**
- **Email addresses** - High confidence detection
- **Phone numbers** - International format support using Google libphonenumber
- **Social Security Numbers (SSN)** - US format detection
- **Credit card numbers** - Basic format detection
- **Dates** - Various date formats
- **IBAN codes** - International bank account numbers
- **Addresses** - Street address detection

### 📄 **Multi-Format Support**
- **PDF files** - Secure redaction with PyMuPDF
- **Word documents (.docx)** - Preserves formatting
- **Text files (.txt)** - Simple text processing

### 🛡️ **Security Features**
- **Irreversible redaction** - Data is permanently removed, not just hidden
- **Confidence scoring** - Each detection includes confidence level (high/medium/low)
- **Manual review** - Users can approve/reject each detected item
- **Secure processing** - Files processed locally, not sent to external services

### 🎨 **User-Friendly Interface**
- **Drag-and-drop upload** - Easy file selection
- **Interactive review** - Visual PII detection results
- **Batch processing** - Select multiple items for redaction
- **Progress indicators** - Real-time processing feedback
- **Responsive design** - Works on desktop and mobile

## Technology Stack

### Backend
- **Flask** - Python web framework
- **PyMuPDF** - PDF processing and redaction
- **python-docx** - Word document processing
- **phonenumbers** - Phone number detection and validation
- **Regular expressions** - Pattern matching for various PII types

### Frontend
- **React** - Modern JavaScript framework
- **Tailwind CSS** - Utility-first CSS framework
- **shadcn/ui** - High-quality UI components
- **Lucide Icons** - Beautiful icon library

## Installation

### Prerequisites
- Python 3.11+
- Node.js 20+
- Virtual environment support

### Backend Setup
```bash
cd document-redactor
source venv/bin/activate
pip install -r requirements.txt
```

### Frontend Setup
```bash
cd redaction-frontend
npm install
npm run build
```

### Integration
```bash
# Copy built frontend to Flask static directory
cp -r redaction-frontend/dist/* document-redactor/src/static/
```

## Usage

### Starting the Application
```bash
cd document-redactor
source venv/bin/activate
python src/main.py
```

The application will be available at `http://localhost:5000`

### Using the Tool

1. **Upload Document**
   - Click the upload area or drag and drop a file
   - Supported formats: PDF, DOCX, TXT (up to 10MB)

2. **Review Detected PII**
   - View all detected sensitive information
   - Each item shows type, confidence level, and preview
   - High-confidence items are pre-selected

3. **Select Items to Redact**
   - Check/uncheck items for redaction
   - Use "Clear All" to deselect everything
   - Review your selections before proceeding

4. **Apply Redactions**
   - Click "Apply Redactions" to process the document
   - Wait for processing to complete

5. **Download Result**
   - Download the redacted document
   - Original formatting is preserved
   - Sensitive data is replaced with [REDACTED]

## API Endpoints

### Upload Document
```
POST /api/redaction/upload
Content-Type: multipart/form-data

Form data:
- file: Document file (PDF/DOCX/TXT)

Response:
{
  "file_id": "uuid",
  "filename": "original_name.pdf",
  "document_type": "pdf",
  "analysis": {
    "pii_items": [...],
    "pages_data": [...] // For PDFs
  }
}
```

### Apply Redactions
```
POST /api/redaction/redact
Content-Type: application/json

{
  "file_id": "uuid",
  "document_type": "pdf",
  "redactions": [
    {
      "text": "john@example.com",
      "type": "email",
      "page": 0 // For PDFs only
    }
  ]
}

Response:
{
  "success": true,
  "output_file": "filename_redacted.pdf",
  "download_url": "/api/redaction/download/filename_redacted.pdf"
}
```

### Download Redacted Document
```
GET /api/redaction/download/{filename}

Returns: File download
```

## Security Considerations

### Data Protection
- Files are processed locally on the server
- No data is sent to external services
- Temporary files are stored in `/tmp` directories
- Original files can be manually deleted after processing

### Redaction Security
- **PDF**: Uses PyMuPDF's secure redaction that removes content from the document structure
- **DOCX**: Replaces text content while preserving document structure
- **TXT**: Direct text replacement

### Limitations
- OCR is not implemented for scanned documents
- Complex PDF layouts may affect redaction accuracy
- Custom PII patterns require code modification

## Development

### Adding New PII Types
Edit `src/routes/redaction.py` and add patterns to the `PIIDetector` class:

```python
def __init__(self):
    # Add new regex pattern
    self.custom_pattern = re.compile(r'your_pattern_here')

def detect_pii(self, text):
    # Add detection logic
    for match in self.custom_pattern.finditer(text):
        pii_items.append({
            'type': 'custom_type',
            'text': match.group(),
            'start': match.start(),
            'end': match.end(),
            'confidence': 'medium'
        })
```

### Frontend Customization
The React frontend is built with Tailwind CSS and shadcn/ui components. Modify `src/App.jsx` to customize the interface.

## Testing

### Test Files Included
- `test_document.txt` - Sample text with various PII types
- `medical_record.pdf` - Sample PDF for testing

### Manual Testing
```bash
# Test upload
curl -X POST -F "file=@test_document.txt" http://localhost:5000/api/redaction/upload

# Test redaction
curl -X POST -H "Content-Type: application/json" \
  -d '{"file_id":"uuid","document_type":"txt","redactions":[...]}' \
  http://localhost:5000/api/redaction/redact
```

## Deployment

### Production Deployment
For production use, consider:
- Using a production WSGI server (gunicorn, uWSGI)
- Setting up proper logging
- Implementing file cleanup routines
- Adding authentication if needed
- Using HTTPS
- Setting up proper error handling

### Environment Variables
```bash
export FLASK_ENV=production
export SECRET_KEY=your_secret_key_here
```

## License

This project is developed as a demonstration tool. Please ensure compliance with your organization's data protection policies before processing sensitive documents.

## Support

For issues or questions about this tool, please refer to the implementation details in the source code or contact the development team.

