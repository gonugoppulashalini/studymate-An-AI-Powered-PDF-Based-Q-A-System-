import PyPDF2
import openai
import os
from dotenv import load_dotenv
import textwrap

class StudyMate:
    def _init_(self, api_key: str = None):
        if api_key:
            openai.api_key = api_key
        else:
            load_dotenv()
            openai.api_key = os.getenv("OPENAI_API_KEY")
            if not openai.api_key:
                raise ValueError("OpenAI API key not found. Set OPENAI_API_KEY in .env or pass api_key parameter")
        self.text_content = ""
    
    def extract_text_from_pdf(self, pdf_path: str) -> str:
        try:
            with open(pdf_path, 'rb') as file:
                pdf_reader = PyPDF2.PdfReader(file)
                self.text_content = "\n".join([page.extract_text() for page in pdf_reader.pages if page.extract_text()])
            print(f"Extracted {len(self.text_content)} characters from PDF")
            return self.text_content
        except Exception as e:
            return f"Error reading PDF: {str(e)}"
    
    def ask_question(self, question: str, max_tokens: int = 300) -> str:
        if not self.text_content:
            return "Please load a PDF first"
        
        prompt = f"Based on this text: {self.text_content[:6000]}\n\nQuestion: {question}\nAnswer:"
        
        try:
            response = openai.ChatCompletion.create(
                model="gpt-3.5-turbo",
                messages=[
                    {"role": "system", "content": "You help students understand study materials. Answer based only on the provided text."},
                    {"role": "user", "content": prompt}
                ],
                max_tokens=max_tokens,
                temperature=0.3
            )
            return response.choices[0].message['content'].strip()
        except Exception as e:
            return f"Error: {str(e)}"
    
    def summarize_document(self, max_tokens: int = 500) -> str:
        if not self.text_content:
            return "Please load a PDF first"
        
        try:
            response = openai.ChatCompletion.create(
                model="gpt-3.5-turbo",
                messages=[
                    {"role": "system", "content": "Create a clear summary of the study materials."},
                    {"role": "user", "content": f"Summarize this: {self.text_content[:6000]}"}
                ],
                max_tokens=max_tokens,
                temperature=0.4
            )
            return response.choices[0].message['content'].strip()
        except Exception as e:
            return f"Error: {str(e)}"

def main():
    try:
        studymate = StudyMate()
        print("StudyMate initialized!")
    except ValueError as e:
        print(f"Error: {e}")
        return
    
    while True:
        print("\n" + "="*40)
        print("STUDYMATE: PDF Q&A System")
        print("1. Load PDF")
        print("2. Ask question")
        print("3. Summarize")
        print("4. Exit")
        
        choice = input("Choose option (1-4): ").strip()
        
        if choice == "1":
            pdf_path = input("PDF path: ").strip()
            if os.path.exists(pdf_path):
                studymate.extract_text_from_pdf(pdf_path)
            else:
                print("File not found")
        
        elif choice == "2":
            if studymate.text_content:
                question = input("Your question: ").strip()
                if question:
                    answer = studymate.ask_question(question)
                    print("\nAnswer:", textwrap.fill(answer, width=80))
            else:
                print("Load a PDF first")
        
        elif choice == "3":
            if studymate.text_content:
                summary = studymate.summarize_document()
                print("\nSummary:", textwrap.fill(summary, width=80))
            else:
                print("Load a PDF first")
        
        elif choice == "4":
            print("Goodbye!")
            break
        else:
            print("Invalid choice")

if __name__ == "_main_":
    main()