import os
import javalang

def extract_request_mapping_service_calls(java_code):
    tree = javalang.parse.parse(java_code)
    method_calls = {}

    for _, node in tree.filter(javalang.tree.MethodDeclaration):
        method_name = node.name
        annotations = [annotation.name for annotation in node.annotations]

        if any(ann in annotations for ann in ['RequestMapping', 'GetMapping', 'PostMapping']):
            service_calls = []
            for path, method_invocation in node.filter(javalang.tree.MethodInvocation):
                if method_invocation.qualifier and 'Service' in method_invocation.qualifier:
                    service_calls.append((method_invocation.qualifier, method_invocation.member))

            if service_calls:
                method_calls[method_name] = service_calls

    return method_calls

def process_java_file(java_file_path):
    with open(java_file_path, 'r', encoding='utf-8') as file:
        java_code = file.read()

    method_calls = extract_request_mapping_service_calls(java_code)

    for method, calls in method_calls.items():
        if any(keyword in method.lower() for keyword in ['insert', 'update', 'delete']):
            relevant_calls = [
                (service, call) for service, call in calls
                if any(keyword in call.lower() for keyword in ['insert', 'update', 'delete'])
            ]
            if relevant_calls:
                if len(relevant_calls) >= 2:
                    print(f"\nFile: {java_file_path}")
                    print(f"Method: {method} (Calls: {len(relevant_calls)})")
                    for service, call in relevant_calls:
                        print(f"  Service: {service}, Method: {call}")

def process_directory(directory):
    for root, _, files in os.walk(directory):
        for file in files:
            if file.endswith('Controller.java'):
                java_file_path = os.path.join(root, file)
                process_java_file(java_file_path)


# 특정 폴더 경로 지정
directory_path = './egovframe-common-components/src/main/java/egovframework'
process_directory(directory_path)
